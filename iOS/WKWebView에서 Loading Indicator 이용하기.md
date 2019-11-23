# WKWebView에서 Loading Indicator 이용하기

`WKWebView`에서 페이지 로딩할 때 보이는 Indicator를 추가해보자!

페이지 로딩할 때만 보이고 로딩이 완료된 후에는 움직임을 멈추고 hidden 처리 한다.

<br />

```swift
//
//  ViewController.swift
//  07-1 WebView
//
//  Created by Soojung Shin on 2019/11/18.
//  Copyright © 2019 Soojung Shin. All rights reserved.
//

import UIKit
import WebKit

class ViewController: UIViewController, WKNavigationDelegate {

    @IBOutlet var txtUrl: UITextField!
    @IBOutlet var myWebView: WKWebView!
    @IBOutlet var myActivityIndicator: UIActivityIndicatorView!
        
    func loadWebPage(_ url: String) {
        let myUrl = URL(string: url)
        let myRequest = URLRequest(url: myUrl!)
        myWebView.load(myRequest)
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        
        myWebView.navigationDelegate = self
        loadWebPage("http://www.naver.com")
    }
    
    
    //webView가 web content를 받기 시작할 때 실행
    func webView(_ webView: WKWebView, didCommit navigation: WKNavigation!) {
        myActivityIndicator.isHidden = false
        myActivityIndicator.startAnimating()
    }
    
    
    //navigation이 완료되었을 때 실행
    func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
        myActivityIndicator.isHidden = true
        myActivityIndicator.stopAnimating()
    }
}
```

<br />

<br />

```swift
class ViewController: UIViewController, WKNavigationDelegate {
```

`ViewController`에 `WKNavigationDelegate`를 상속

<br />

```swift
myWebView.navigationDelegate = self
```

대리자 위임 필수!

<br />

```swift
//webView가 web content를 받기 시작할 때 실행
func webView(_ webView: WKWebView, didCommit navigation: WKNavigation!) {
  myActivityIndicator.isHidden = false
  myActivityIndicator.startAnimating()
}


//navigation이 완료되었을 때 실행
func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
  myActivityIndicator.stopAnimating()
  myActivityIndicator.isHidden = true
}
```

<br />

이 외에도 `WKNavigationDelegate`에는 여러가지 메소드가 있음 → [Apple Developer Document](https://developer.apple.com/documentation/webkit/wknavigationdelegate) 참고!
