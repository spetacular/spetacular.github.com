---
layout: post
title: Swift知识点纪录
---
po主初学Swift，遇到不少细节知识的缺失，只好请教Google大神。这篇文就是Google的结果，纪录一下。

##获取UNIX时间戳
即获得到某个时间点到1970年1月1日0点0分0秒的秒数。

返回字符串格式：

     var Timestamp: String {
        return "\(NSDate().timeIntervalSince1970)"
    }

输出时，可以这样调用：
    
    println("Timestamp: \(Timestamp)")

如果希望返回NSTimeInterval，则：
  
    var Timestamp: NSTimeInterval {
        return NSDate().timeIntervalSince1970
    }

##创建alert
创建Alert的代码：

<code>
import UIKit

class ViewController: UIViewController, UIAlertViewDelegate {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }

    @IBAction func buttonPressed(sender : AnyObject) {
        var createAccountErrorAlert: UIAlertView = UIAlertView()

        createAccountErrorAlert.delegate = self

        createAccountErrorAlert.title = "Oops"
        createAccountErrorAlert.message = "Could not create account!"
        createAccountErrorAlert.addButtonWithTitle("Dismiss")
        createAccountErrorAlert.addButtonWithTitle("Retry")

        createAccountErrorAlert.show()
    }

    func alertView(View: UIAlertView!, clickedButtonAtIndex buttonIndex: Int) {

        switch buttonIndex {

        default:
            println("alertView \(buttonIndex) clicked")

        }
    }
}
</code>