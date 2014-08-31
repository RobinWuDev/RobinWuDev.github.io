---
layout: post
title: "延时操作"
date: 2014-08-31 18:52:08 +0800
comments: true
categories: iOS
---
在开发过程中，我们经常会遇到延迟操作，例如在网络请求成功后，自动返回前一页，如果请求完成马上返回，会很快，体验很不好，所以这时候我们做一个延时操作，等0.3S后再执行操作。<!--more-->

###1.使用performSelector

`[self performSelector:@selector(pBack) withObject:nil afterDelay:2];`

很多人采用的是这样的代码，但是这样的代码会产生一些问题。例如例如我定时2S后返回，而用户1S的时候就点击返回了，这时候会出现怎样的情况？

	- (void)viewDidLoad
	{
	    [super viewDidLoad];
	    // Do any additional setup after loading the view.
	    self.view.backgroundColor = [UIColor redColor];
	    [self performSelector:@selector(pBack) withObject:nil afterDelay:1];
	    [self performSelector:@selector(pBack) withObject:nil afterDelay:2];
	}
	
	- (void)didReceiveMemoryWarning
	{
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	}
	
	- (void)dealloc {
	    NSLog(@"dealloc");
	}
	
	#pragma mark - private method
	- (void)pBack {
	    NSLog(@"pBack");
	    [self dismissViewControllerAnimated:YES completion:nil];
	} 
	 
我用两个*performSelector*模拟了用户的操作，一个是2s后返回，一个是1s后返回，这是我们看下执行log。

	2014-08-31 19:20:30.221 DelayDemo[56875:60b] pBack
	2014-08-31 19:20:31.221 DelayDemo[56875:60b] pBack
	2014-08-31 19:20:31.222 DelayDemo[56875:60b] dealloc

从结果看两次都有执行，然而*dealloc*等到第二次执行完后才执行。这时候我们可能就会觉得有问题了，因为他跟我们的预期是不一致的，我们一般觉得第一次结束后就应该执行dealloc操作，结果不是的。因为调用*performSelector*会retain一次，所以当执行*[self dismissViewControllerAnimated:YES completion:nil];*的时候，retainCount并不为0，所以不会销毁，所以如果开发者觉得调用pBack后，对象就会被销毁，那么就会出现一些bug。    
例如:

	- (void)viewDidLoad
	{
	    [super viewDidLoad];
	    // Do any additional setup after loading the view.
	    self.view.backgroundColor = [UIColor redColor];
	    [self performSelector:@selector(pBack) withObject:nil afterDelay:1];
	    [self performSelector:@selector(pBack) withObject:nil afterDelay:5];
	    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(pNotification:) name:@"RBTest" object:nil];
	    
	}
	
	- (void)didReceiveMemoryWarning
	{
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	}
	
	- (void)dealloc {
	    [[NSNotificationCenter defaultCenter]removeObserver:self];
	    NSLog(@"dealloc");
	}
	
	#pragma mark - private method
	- (void)pBack {
	    NSLog(@"pBack");
	    [self.navigationController popViewControllerAnimated:YES];
	}
	
	- (void)pNotification:(NSNotification *)pNote {
	    [self performSegueWithIdentifier:@"toOne" sender:nil];
	}
	
上面的代码，当用户返回后，因为还有一个*performSelector*没执行完，那么Dealloc不会被调用，所以还是可以收到通知，如果这时候收到一个通知，要去执行一个push操作，程序就会崩溃。

怎么处理需要在返回的时候取消掉那么些还没有执行完的操作

`[NSObject cancelPreviousPerformRequestsWithTarget:self];`

所以pBack方法的代码就变成这样了。

	- (void)pBack {
	    NSLog(@"pBack");
	    [NSObject cancelPreviousPerformRequestsWithTarget:self];
	    [self.navigationController popViewControllerAnimated:YES];
	}

###2.使用GCD    

事实上我是比较推荐用GCD，因为代码更紧凑，也更简单，然后封装一下会易用。

	- (void)viewDidLoad
	{
	    [super viewDidLoad];
	    // Do any additional setup after loading the view.
	    self.view.backgroundColor = [UIColor redColor];
	//    [self performSelector:@selector(pBack) withObject:nil afterDelay:1];
	//    [self performSelector:@selector(pBack) withObject:nil afterDelay:5];
	    __weak QYToViewController *this = self;
	    dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC));
	    dispatch_after(time, dispatch_get_main_queue(), ^{
	        [this pBack];
	    });
	    
	    dispatch_time_t time1 = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5.0 * NSEC_PER_SEC));
	    dispatch_after(time1, dispatch_get_main_queue(), ^{
	        [this pBack];
	    });
	    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(pNotification:) name:@"RBTest" object:nil];
	    
	}

相比于使用*performSelector*，显然使用GCD更简单，如果我们把这些代码封装下.

	- (void)viewDidLoad
	{
	    [super viewDidLoad];
	    // Do any additional setup after loading the view.
	    self.view.backgroundColor = [UIColor redColor];
	//    [self performSelector:@selector(pBack) withObject:nil afterDelay:1];
	//    [self performSelector:@selector(pBack) withObject:nil afterDelay:5];
	    __weak QYToViewController *this = self;
	    [self rbDelayTime:1 usingBlock:^{
	        [this pBack];
	    }];
	    [self rbDelayTime:5 usingBlock:^{
	        [this pBack];
	    }];
	    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(pNotification:) name:@"RBTest" object:nil];
	    
	}
	
	
	- (void)rbDelayTime:(NSTimeInterval)pTime usingBlock:(dispatch_block_t) pBlock {
	    dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(pTime * NSEC_PER_SEC));
	    dispatch_after(time, dispatch_get_main_queue(),pBlock);
	}
	
封装后显然更易用，但是使用GCD有一个问题，就是无法取消。

当我们在开发过程中，在使用延时操作都要小心点，所以每当我们讨论说要加个延时或者定时，我都要把心悬起来，我们永远不会知道在等待过程中会发生什么！