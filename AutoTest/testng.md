# TestNG

> TestNG是一个测试Java应用程序的开源框架，类似JUnit和NUnit。

[TOC]

## 环境配置

> TestNG是一个测试Java应用程序的开源框架，类似JUnit和NUnit。

## 环境配置

*  配置好java环境，命令行`java -version`查看
*  [官网](http://testng.org/doc/download.html) , 下载对应系统下jar文件
*  系统环境变量中添加指向jar文件所在路径
*  Eclipse中安装testng，Help -> Install New Software，Add [http://beust.com/eclipse](http://beust.com/eclipse)

## TestNG 基本用法

````java
import org.junit.AfterClass;
import org.junit.BeforeClass;
import org.testng.annotations.Test;

public class TestNGLearn1 {

    @BeforeClass
    public void beforeClass() {
        System.out.println("this is before class");
    }

    @Test
    public void TestNgLearn() {
        System.out.println("this is TestNG test case");
    }

    @AfterClass
    public void afterClass() {
        System.out.println("this is after class");
    }
}
````

## TestNG 基本注解

| 注解        | 描述     |  
| --------   | -----:  |
| **@BeforeSuite**     |注解的方法将只运行一次，运行所有测试前此套件中 |
| **@AfterSuite**        |  注解的方法将只运行一次此套件中的所有测试都运行之后   |
|**@BeforeClass**| 注解的方法将只运行一次先行先试在当前类中的方法调用 |
|**@AfterClass** | 注解的方法将只运行一次后已经运行在当前类中的所有测试方法 |
|**@BeforeTest** |注解的方法将被运行之前的任何测试方法属于内部类的 <test>标签的运行 |
|**@AfterTest**| 注解的方法将被运行后，所有的测试方法，属于内部类的<test>标签的运行 | 
|**@BeforeGroups**|组的列表，这种配置方法将之前运行。此方法是保证在运行属于任何这些组第一个测试方法，该方法被调用|
|**@AfterGroups**|组的名单，这种配置方法后，将运行。此方法是保证运行后不久，最后的测试方法，该方法属于任何这些组被调用|
|**@BeforeMethod**|注解的方法将每个测试方法之前运行|
|**@AfterMethod**|被注释的方法将被运行后，每个测试方法|
|**@DataProvider**|标志着一个方法，提供数据的一个测试方法。注解的方法必须返回一个Object[] []，其中每个对象[]的测试方法的参数列表中可以分配。该@Test 方法，希望从这个DataProvider的接收数据，需要使用一个dataProvider名称等于这个注解的名字|
|**@Factory**|作为一个工厂，返回TestNG的测试类的对象将被用于标记的方法。该方法必须返回Object[]|
|**@Listeners**|定义一个测试类的监听器|
|**@Parameters**|将xml文件中参数传递给@Test方法|
|**@Test**|标记一个类或方法作为测试的一部分|

## testng.xml

|属性      | 描述        |
|--------   | --------|
|name|suite的名字（他会出现在测试报告中）|
|junit| 是否以junit模式运行|
|verbose|在控制台中如何输出，这个设置不影响html版本的测试报告|
|parallel | 是否使用多线程测试（可加速测试）|
|configfailurepolicy | 是否在运行失败了一次后继续尝试或跳过|
| thread-count | 如果设置了parallel，可以设置线程数|
| annotations|  有‘javadoc’的时候寻找，没有的话使用jdk5的注释|
|time-out | 在终止method (如果parallel="methods") 或者test (如果parallel="tests")之前设置以毫秒为单位的等待时间|
|skipfailedinvocationcounts|是否跳过失败的调用|
|data-provider-thread-count|提供一个整数线程池的范围为了使用parallel data|
|object-factory| 一个继承IObjectFactory的类，被用来实例化测试对象|
|allow-return-values|如果设置true，将会运行测试用例并返回值|

**示例**
````java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd" >
<suite name="Suite" parallel="tests" thread-count="5">
	<test name="Test" preserve-order="true" verbose="2">
	    <parameter name="userName" value="15952031403"></parameter>
	    <parameter name="originPwd" value="c12345"></parameter>
		<classes>
			<class name="com.unionpay.testng.UPPayRegisterTest">
			</class>
		</classes>
	</test>
	<!-- <test name="Test1" preserve-order="true">
	    <classes>
	    <class name="com.unionpay.testng.Test2">
			</class>
		</classes>
	</test>
	<test name="Test2" preserve-order="true">
	    <classes>
	    <class name="com.unionpay.testng.Test3">
			</class>
		</classes>
	</test> -->
</suite> <!-- Suite -->
````
- `parallel`和`thread-count`分别指定并行化测试的范围(`tests`, `methods`、`classes`)和并行线程数
- `preserve-order`，设为true时，节点下的方法按顺序执行
-  `verbose` 表示记录的日志级别，在*0-10*之间取值
- `<parameter name="userName", value="1595203xxxx"/>` 给测试代码传递参数键值对，在测试类中通过注解`@Parameters({"userName","originPwd"})`获取

## 参数化测试
> 当测试逻辑一样，只是参数不一样时，采用数据驱动测试机制，避免写重复代码。TestNG中通过`@DataProvider`实现数据驱动。  
  
利用@DataProvider做数据驱动，数据源文件可以是EXCEL，XML，甚至可以是TXT文本。以读取xml文件为例，通过@DataProvider读取XML文件中数据，然后测试方法只要标示获取数据来源的DataProvider，那么对应的DataProvider会把读取的数据传给该test方法。
![DataProvider原理](http://upload-images.jianshu.io/upload_images/1237796-4868bbfd804b3d08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1. 构建xml数据文件
````xml
<?xml version="1.0" encoding="UTF-8"?>
<data>
    <login>
        <username>user1</username>
        <password>123456</password>
    </login>
    <login>
        <username>user2</username>
        <password>345678</password>
    </login>
</data>
````
### 2. 读取xml文件
````java
import java.io.File;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Map;

import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

public class ParseXml {

    /**
     * 利用Dom4j解析xml文件，返回list
     * @param xmlFileName
     * @return
     */
    public static List parse3Xml(String xmlFileName){
	File inputXml = new File(xmlFileName);    
        List list= new ArrayList();                
        int count = 1;
        SAXReader saxReader = new SAXReader();
        try {
            Document document = saxReader.read(inputXml);
            Element items = document.getRootElement();
            for (Iterator i = items.elementIterator(); i.hasNext();) {
                Element item = (Element) i.next();
                Map map = new HashMap();
                Map tempMap = new HashMap();
                for (Iterator j = item.elementIterator(); j.hasNext();) {
                    Element node = (Element) j.next();                    
                    tempMap.put(node.getName(), node.getText());                    
                }
                map.put(item.getName(), tempMap);
                list.add(map);
            }
        } catch (DocumentException e) {
            System.out.println(e.getMessage());
        }
        System.out.println(list.size());
        return list;
    }    
}
````
### 3. DataProvider类
````java
import java.lang.reflect.Method;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

import org.testng.Assert;
import org.testng.annotations.DataProvider;

public class GenerateData {
    
    public static List list = new ArrayList();
    
    @DataProvider(name = "dataProvider")
    public static Object[][] dataProvider(Method method){      
	list = ParseXml.parse3Xml("absolute path of  xml file");
        List<Map<String, String>> result = new ArrayList<Map<String, String>>();        
        for (int i = 0; i < list.size(); i++) {
            Map m = (Map) list.get(i);    
            if(m.containsKey(method.getName())){                            
                Map<String, String> dm = (Map<String, String>) m.get(method.getName());
                result.add(dm);    
            }
        }  
        if(result.size() > 0){
            Object[][] files = new Object[result.size()][];
            for(int i=0; i<result.size(); i++){
                files[i] = new Object[]{result.get(i)};
            }        
            return files;
        }else {
            Assert.assertTrue(result.size()!=0,list+" is null, can not find"+method.getName() );
	    return null;
	}
    }

}
````
### 4. 在test方法中引用dataprovider
````java
public class LoginTest {
 @Test(dataProvider="dataProvider", dataProviderClass= GenerateData.class)
    public  void login(Map<String, String> param) throws InterruptedException{

        List<WebElement> edits = findElementsByClassName(AndroidClassName.EDITTEXT);
        edits.get(0).sendkeys(param.get("username"));
        edits.get(1).sendkeys(param.get("password"));
    }
}
````
> xml中的父节点与test方法名对应，因此xml中同名父节点的个数就意味着该test方法会被重复执行多少次； 当dataprovider与test方法不在同一个类时，需指明dataprovider类，如`dataProviderClass= GenerateData.class`

## TestNG重写监听类
> TestNG会监听每个测试case的运行结果，有时候我们需要定制一些其他功能，如自动截图，发送数据给服务器等。方法是新建一个继承TestListenerAdapter的类。
重写完成后，在需要的test方法前添加注解`@Listeners(TestNGListener.class)`

````java
package com.unionpay.listener;

import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import org.testng.ITestContext;
import org.testng.ITestResult;
import org.testng.TestListenerAdapter;

import com.google.gson.Gson;
import com.google.gson.JsonObject;
import com.unionpay.base.BaseTest;
import com.unionpay.constants.CapabilitiesBean;
import com.unionpay.constants.CaseCountBean;
import com.unionpay.constants.ResultBean;
import com.unionpay.util.Assertion;
import com.unionpay.util.PostService;
import com.unionpay.util.ReadCapabilitiesUtil;

/**
 * 带有post请求的testng监听
 * @author lichen2
 */
public class TestNGListenerWithPost extends TestListenerAdapter{
    
    //接收每个case结果的接口
    private String caseUrl;
    
    //接收整个test运行数据的接口
    private String countUrl;
    
    //接收test运行状态的接口
    private String statusUrl;
    
    private JsonObject caseResultJson = new JsonObject();
    
    private JsonObject caseCountJson = new JsonObject();
    
    private Gson gson = new Gson();
    
    private ResultBean result = new ResultBean();
    
    private CaseCountBean caseCount = new CaseCountBean();
    
    private SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    
    private CapabilitiesBean capabilitiesBean = ReadCapabilitiesUtil.readCapabilities("setting.json");
    
    private String testStartTime;
    
    private String testEndTime;
    
    private String runId;
    
    //testng初始化
    @Override
    public void onStart(ITestContext testContext) {
        super.onStart(testContext);
        String serverUrl = capabilitiesBean.getServerurl();
	caseUrl = "http://"+serverUrl+"/api/testcaseResult";
	countUrl = "http://"+serverUrl+"/api/testcaseCount";
	statusUrl = "http://"+serverUrl+"/api/testStatus";
        runId = capabilitiesBean.getRunid();
        result.setRunId(runId);
        caseCount.setRunId(runId);
    }
    
    //case开始
    @Override
    public void onTestStart(ITestResult tr) {
	Assertion.flag = true;
	Assertion.errors.clear();
	sendStatus("运行中");
	result.setStartTime(format.format(new Date()));
    }
    
    //case成功执行
    @Override
    public void onTestSuccess(ITestResult tr) {
        super.onTestSuccess(tr);
        sendResult(tr);
        takeScreenShot(tr);
    }

    //case执行失败
    @Override
    public void onTestFailure(ITestResult tr) {
        super.onTestFailure(tr);
        sendResult(tr);
        try {
            takeScreenShot(tr);
        } catch (SecurityException e) {
            e.printStackTrace();
        } catch (IllegalArgumentException e) {         
            e.printStackTrace();
        }
        this.handleAssertion(tr);
    }

    //case被跳过
    @Override
    public void onTestSkipped(ITestResult tr) {
        super.onTestSkipped(tr);
        takeScreenShot(tr);
        sendResult(tr);
        this.handleAssertion(tr);
    }

    //所有case执行完成
    @Override
    public void onFinish(ITestContext testContext) {
        super.onFinish(testContext);
        sendStatus("正在生成报告");
        sendFinishData(testContext);
    }
    
    /**
     * 发送case测试结果
     * @param tr
     */
    public void sendResult(ITestResult tr){
	result.setTestcaseName(tr.getName());
	result.setEndTime(format.format(new Date()));
	float tmpDuration = (float)(tr.getEndMillis() - tr.getStartMillis());
	result.setDuration(tmpDuration / 1000);
	
	switch (tr.getStatus()) {
	case 1:
	    result.setTestResult("SUCCESS");
	    break;
	case 2:
	    result.setTestResult("FAILURE");
	    break;
	case 3:
	    result.setTestResult("SKIP");
	    break;
	case 4:
	    result.setTestResult("SUCCESS_PERCENTAGE_FAILURE");
	    break;
	case 16:
	    result.setTestResult("STARTED");
	    break;
	default:
	    break;
	}
	caseResultJson.addProperty("result", gson.toJson(result));
	PostService.sendPost(caseUrl, caseResultJson.toString());
    }
    
    /**
     * 通知test完成
     * @param testContext
     */
    public void sendFinishData(ITestContext tc){
	testStartTime = format.format(tc.getStartDate());
	testEndTime = format.format(tc.getEndDate());
	long duration = getDurationByDate(tc.getStartDate(), tc.getEndDate());
	caseCount.setTestStartTime(testStartTime);
	caseCount.setTestEndTime(testEndTime);
	caseCount.setTestDuration(duration);
	caseCount.setTestSuccess(tc.getPassedTests().size());
	caseCount.setTestFail(tc.getFailedTests().size());
	caseCount.setTestSkip(tc.getSkippedTests().size());
	
	caseCountJson.addProperty("count", gson.toJson(caseCount));
	PostService.sendPost(countUrl, caseCountJson.toString());
    }
    
    /**
     * 通知test运行状态
     */
    public void sendStatus(String status){
	JsonObject jsonObject = new JsonObject();
	jsonObject.addProperty("runId", runId);
	jsonObject.addProperty("status", status);
	JsonObject sendJson = new JsonObject();
	sendJson.addProperty("status", jsonObject.toString());
	PostService.sendPost(statusUrl, sendJson.toString());
    }
    
    //计算date间的时差（s）
    public long getDurationByDate(Date start, Date end){
	long duration = end.getTime() - start.getTime();
	return duration / 1000;
    }

    //截图
    private void takeScreenShot(ITestResult tr) {
        BaseTest b = (BaseTest) tr.getInstance();
        b.takeScreenShot(tr);
    }
}
````
** BaseTest **
````java
package com.unionpay.base;

import org.testng.ITestResult;
import com.unionpay.listener.TestNGListenerWithPost;
@Listeners(TestNGListenerWithPost.class)
public abstract class BaseTest {
    public AndroidDriver<WebElement> driver;
    public BaseTest() {
	driver = DriverFactory.getDriverByJson();
    }

    /**
     * 截屏并保存到本地
     * @param tr
     */
    public void takeScreenShot(ITestResult tr) {
	String fileName = tr.getName() + ".jpg";
	File dir = new File("target/snapshot");
	if (!dir.exists()) {
	    dir.mkdirs();
	}
	String filePath = dir.getAbsolutePath() + "/" + fileName;
	if (driver != null) {
	    try {
		File scrFile = driver.getScreenshotAs(OutputType.FILE);
		FileUtils.copyFile(scrFile, new File(filePath));
	    } catch (IOException e) {
		e.printStackTrace();
	    }
	}
    }
}
````