# Uotification


         #pragma mark ---  推送注册
          - (void)uotificationCenter:(UIApplication *)application{
               if ([[UIDevice currentDevice].systemVersion floatValue] >= 10.0) {
           //iOS10特有
                UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter]; // 必须写代理，不然无法监听通知的接收与点击
                center.delegate = self;
                [center requestAuthorizationWithOptions:(UNAuthorizationOptionAlert | UNAuthorizationOptionBadge | UNAuthorizationOptionSound) completionHandler:^(BOOL granted, NSError * _Nullable error) {
             if (granted) {
                   NSLog(@"注册成功");
                    [center getNotificationSettingsWithCompletionHandler:^(UNNotificationSettings * _Nonnull settings) {
                      NSLog(@"%@", settings);
                   }];
                } else {
                    NSLog(@"注册失败");
                 }
              }];
                   }else if ([[UIDevice currentDevice].systemVersion floatValue] >= 8.0){
               //iOS8 - iOS10
               [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert | UIUserNotificationTypeSound | UIUserNotificationTypeBadge categories:nil]];
                } // 注册获得device Token
                   [[UIApplication sharedApplication] registerForRemoteNotifications];
                }
                
                
              
              // iOS 10收到通知
            - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler{



              }
              
              
              
              
              
              /**
 *
 *解决本地推送 创建多条问题
 *获取所有本地推送
 *找到该key 的通知
 如果有不再创建新的推送
 */
- (void)addloalNotification{
    // 获取所有本地通知数组
    //[[UIApplication sharedApplication] cancelAllLocalNotifications];//删除所有的本地推送
    
    NSArray *localNotifications = [UIApplication sharedApplication].scheduledLocalNotifications;
    if (localNotifications.count >0 ) {
        
        for (UILocalNotification *notification in localNotifications) {
            NSDictionary *userInfo = notification.userInfo;
            if (userInfo) {
                // 根据设置通知参数时指定的key来获取通知参数
                NSString *info = userInfo[@"worldKey"];
                if (![info isEqualToString:@"keyworld"]) {
                    [[UIApplication sharedApplication] scheduleLocalNotification:self.localNotification];
                }
            }
        }
    }else{
        [[UIApplication sharedApplication] scheduleLocalNotification:self.localNotification];
    }
}

- (UILocalNotification *)localNotification{
    if (!_localNotification) {
        
        _localNotification = [[UILocalNotification alloc] init];
        //设置时区（跟随手机的时区）
        _localNotification.timeZone = [NSTimeZone defaultTimeZone];
        if (_localNotification) {
            _localNotification.alertTitle = @"待上传任务提醒";
            _localNotification.alertBody = @"请查看是否存在未上传的任务";
            _localNotification.alertAction = @"打开";
            _localNotification.soundName = UILocalNotificationDefaultSoundName;
            //小图标数字
            _localNotification.applicationIconBadgeNumber = 1;
            NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
            [formatter setDateFormat:@"HH:mm:ss"];
            NSDate *date = [formatter dateFromString:@"17:30:00"];
            //通知发出的时间
            _localNotification.fireDate = date;
        }
        //循环通知的周期
        _localNotification.repeatInterval = kCFCalendarUnitDay;
        //设置userinfo方便撤销
        NSDictionary *info = [NSDictionary dictionaryWithObject:@"keyworld" forKey:@"worldKey"];
        _localNotification.userInfo = info;
    }
    return _localNotification;
}
