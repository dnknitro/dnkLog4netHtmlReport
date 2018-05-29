# dnkLog4netHtmlReport

## Description
Library to generate an HTML report based on [log4net](https://logging.apache.org/log4net/) records during application or tests ([NUnit](http://nunit.org/)) execution.

## Installation
It can be installed through [nuget.org](https://www.nuget.org/packages/dnkLog4netHtmlReport/).
During package installation it adds two files to the project:
 * *log4net.config* is an XML file containing configuration for log4net library. Its property "Copy to Output Directory" is set to "Copy if newer". The vital part of the file is HtmlReportAppender which logs log4net records into an HTML report.
 * *GlobalTestSetup.cs* is a NUnit OneTimeSetUp implementation which sets up HTML report title, category, and environment values. If project dnkLog4netHtmlReport is installed into is console application - call to `dnkLog4netHtmlReport.Config.Configure()` method should be made at the beginning of the Main() method.
 * Also *Properties\AssemblyInfo.cs* file is updated: `[assembly: log4net.Config.XmlConfigurator(ConfigFile = "log4net.config")]` is added to the end of the file

## Configuration
At the beginning of application/tests execution global report configuration should be invoked:
```C#
var baseDirectory = AppDomain.CurrentDomain.BaseDirectory.Trim('\\');
dnkLog4netHtmlReport.Config.Configure(
	Path.Combine(Directory.GetParent(baseDirectory).Parent.Parent.Parent.FullName, "Results"),
	new ReportMetaData
	{
		ReportName = "Test Execution Report TEST",
		ReportCategory = "Test",
		ReportEnvironment = "DEV"
	}
);
```

In the example above report HTML report folder location is specified:
```C#
Path.Combine(Directory.GetParent(baseDirectory).Parent.Parent.Parent.FullName, "Results")
```
which is located 3 parant folders up the folder tree related to `\Bin\Debug` folder.
If the Debug folder location is `C:\Projects\MySolution\MyProject.Tests\Bin\Debug` then the Results folder location will be `C:\Projects\MySolution\Results`.

Additionally "browser" can be specified for tests which use Selenium WebDriver:
```C#
[Test]
public void TestAppend1(int index)
{
	Config.SetBrowser("IE");
  ...
}
```

## Usage
To add record to the report simply create an instance of log4net logger `var log = LogManager.GetLogger("myLoggerName");` and then call `log.Info("My log entry");`.
Besides standard log4net levels the library adds a few [extension methods](https://github.com/dnknitro/dnkLog4netHtmlReport/blob/master/src/dnkLog4netHtmlReport/LogExtensions.cs) `Pass()` and `Fail()` which can be used just like standard levels: `log.Pass("Foo found!");`.

To add browser screenshot (when using with Selenium WebDriver) to the report record an additional package [dnkLog4netHtmlReport.SeleniumWebDriver](https://www.nuget.org/packages/dnkLog4netHtmlReport.SeleniumWebDriver/) should be installed. 
Then method `LogWithScreenshot()` can be invoked with desired Log Level and message:
```C#
log.LogWithScreenshot(webDriver, LogLevel.Pass, "Log with screenshot");
```
