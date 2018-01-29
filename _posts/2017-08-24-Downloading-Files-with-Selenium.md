Many platforms you want to extract data from will provide CSV or Excel files.  Manual download is
easy, but doing it everyday is laborious.  Furthermore, if you work for a media company, you might have 100's
of Facebook Pages, YouTube Channels, and so on.  At one point, an automated solution could be beneficial!

One issue I faced was: Where is the file downloading to?

You might develop your script on a MacBook Pro, and ultimately run it on a Linux Ubuntu machine
in the cloud.  Maybe at one point, the data engineering team takes the script over and it
needs to work on a Windows machine.  Fortunately, there is a way to specify where you'd like
files to download, ensuring that the script can be better automated and controlled.

```python
# Choose saving location (Chrome)
chromeOptions = webdriver.ChromeOptions()
prefs = {"download.default_directory" : "/some/path"}
chromeOptions.add_experimental_option("prefs",prefs)
chromedriver = "path/to/chromedriver.exe"
driver = webdriver.Chrome(executable_path=chromedriver, chrome_options=chromeOptions)
```
