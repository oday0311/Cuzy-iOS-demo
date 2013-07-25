late update 2013.7.23
1. fix the tmall jump bug.
2. add change pic size function

///////////////////////////////


lipo -create libCuzyAdSDK.a libCuzyAdSDK_device.a -output libCuzyAdSDK_universal.a
===============version 3.0 ===============
为了解决天猫物品的跳转问题，在使用 webview展示商品detail的时候。，需要重新构造一下跳转连接，代码如下。
- (void)webViewDidFinishLoad:(UIWebView *)webView
{
    [self.loadingImage setHidden:YES];
    
    NSLog(@"webview fininsh loading %@", [webView.request.URL absoluteString]);
    NSString* absoluteString = [webView.request.URL absoluteString];
    if ([absoluteString rangeOfString:@"http://detail.tmall.com/"].length>0) {
        /////this is a web version url of tmall, need to converse to mobile version url
        //http://a.m.tmall.com/i14568464658.htm
        
        NSArray* substrings = [absoluteString componentsSeparatedByCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"?&"]];
        
        
        
        @try {
            NSString* idstring = [substrings objectAtIndex:1];
            NSString* subIdString = [idstring substringFromIndex:3];
            NSString* wapString = [@"" stringByAppendingFormat:@"http://a.m.tmall.com/i%@.htm",subIdString];
            urlString = wapString;
            NSURL* url = [NSURL URLWithString:[self.urlString stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding]];
            if (url)
            {
                NSURLRequest* request = [NSURLRequest requestWithURL:url];
                if (request)
                {
                    [self.webview loadRequest:request];
                }
            }
        }
        @catch (NSException *exception) {
            ///todo
        }
        
    }

}


===========================================
1.  this is a SDK for taobaoke

2.  you can go to http://www.cuzy.com for more details

3.  Something Notable:
    1).Copy the apropriate architecture's .a library and the apropriate resource bundle when you test.
    2).You app's mainWindow's rootViewController should not be nil.
    3).Add -ObjC linker flag to your other link flag.

4.HOW TO USE:
#import "CuzyAdSDK.h"
@interface FrontViewController : UIViewController<CuzyAdSDKDelegate>

{
    
}



- (void)viewDidLoad
{
    [super viewDidLoad];
    [[CuzyAdSDK sharedAdSDK] setDelegate:self];
    [[CuzyAdSDK sharedAdSDK] registerAppWithAppKey:@"cuzy key"     andAppSecret:@"cuzy secret"];
}


- (IBAction)showLayout1:(id)sender {
    [[CuzyAdSDK sharedAdSDK] fetchAppItemWithThemeID:nil orSearchKeywords:@"男鞋"];
}
- (IBAction)showLayout2:(id)sender {
    [[CuzyAdSDK sharedAdSDK] fetchAppItemWithThemeID:@"8" orSearchKeywords:@"手机"];
}
- (IBAction)getRawDataArray:(id)sender {
   //avail able picsize can be check at API head .....
    [[CuzyAdSDK sharedAdSDK] setRawItemPicSize:@"250x250"];

   NSArray* rawArray  = [[CuzyAdSDK sharedAdSDK] fetchRawItemArraysWithThemeID:@"8" orSearchKeywords:@"夹克" withPageIndex:0];
    
    NSLog(@"the rawArray size is %d", rawArray.count);
    for (int i =0;i<[rawArray count]; i++) {
        CuzyTBKItem* tempItem = [rawArray objectAtIndex:i];
        
    }

}


