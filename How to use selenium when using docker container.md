## How to use selenium when using docker container

###TL;DR

Use [zalenium](https://github.com/zalando/zalenium)(for video recording) or [docker-selenium](https://github.com/SeleniumHQ/docker-selenium), follow the docs, pay attantion to

- container port connection
- liveserver if you are using django 
- --network set to your host network
run the command

        docker run --network your_container_network --rm -ti --name zalenium -p 4444:4444 -p 5555:5555 \
        -v /var/run/docker.sock:/var/run/docker.sock \
        -v /tmp/videos:/home/seluser/videos \
        --privileged dosel/zalenium start

use 
        self.driver = webdriver.Remote(
            command_executor='http://zalenium:4444/wd/hub',
            desired_capabilities=DesiredCapabilities.FIREFOX)
to connect the remote server then everything is done.
    
###Long version: 

Althought my project use docker, but at I'm use selenium with docker, and just try to follow the [Selenium docs](https://selenium-python.readthedocs.io/getting-started.html) do it by myself.

first just test seleniu in my laptop, create a virtual environment
    virtualenv -p python3 selenium_test 
active it
    cd selenium_test
    source bin/activate
create a python file for test

    touch test_for_fun.py
in test_for_fun
    
    from selenium import webdriver
    
    driver = webdriver.Firefox()
    driver.get("http://www.python.org")
    print(driver.title)
    assert("Python" in driver.title)

run it

    python test_for_fun.py
    
If your laptop have GUI, everything should work well, run the test then exit().Now I try to config it with my docker project, I using Debian as my server. Things become intersting.
! [fun](img)

Before spend dozen of time to debug this test, first you should make sure do you really need a real browser to run the test, actually, you have some options.

- Use [PhantomJS](http://phantomjs.org/) 
- Use [Headless Chromium](https://developers.google.com/web/updates/2017/04/headless-chrome) 
- Use [Xvfb](https://en.wikipedia.org/wiki/Xvfb)

I read some article about Phantom and Xvfb, like [this](https://news.ycombinator.com/item?id=8418071) and [this](https://watirmelon.blog/2015/12/08/real-vs-headless-browsers-for-automated-acceptance-tests/)

    >the problem with Phantom.js is that compared to an actual browser it still falls short.
    >Headless browsers make it harder to write/debug tests.

Hard to debug? If you wannt test a big project or maybe important to you ,I guess you have to choose the third one, Thanks to the great job from the awesome programmer, we don't have to do much but some easy setup. First install Xvfb in server

    #install Xvfb and firefox
    sudo apt-get install update && apt-get install firefox xvfb

if you get the message like this

you have to add this to the /etc/apt/source.list

    deb http://mozilla.debian.net/ jessie-backports firefox-release

then run 
    
    cd ~/ 
    wget mozilla.debian.net/pkg-mozilla-archive-keyring_1.1_all.deb 
    dpkg -i pkg-mozilla-archive-keyring_1.1_all.deb
after that 
    
    apt-get update && apt-get install -y xvfb -t jessie-backports firefox

If you are using python as I do, you can install [PyVirtualDisplay](https://pypi.python.org/pypi/PyVirtualDisplay), If you are not using python, you can try this solution by [A.J](https://stackoverflow.com/questions/6183276/how-do-i-run-selenium-in-xvfb), in your terminal, everytime after you restart you machine run

    xhost +
and make sure

    export DISPLAY=:0.0 
in your /etc/environment file

befor run the code you should download [geckodriver](https://github.com/mozilla/geckodriver/releases) or [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/downloads) that match your docker container version then add them to your container and specify its path like

    echo PATH=$PATH:/geckodriver_container_path
or tell webdriver the location of firefox in your code 

    from selenium.webdriver.firefox.firefox_binary import FirefoxBinary
    
    binary = FirefoxBinary('geckodriver_container_path')
    driver = webdriver.Firefox(firefox_binary=binary)
If you don't know how to add geckodriver in your docker container, you may have a look at [ADD](https://docs.docker.com/engine/reference/builder/#add) or [VOLUME](https://docs.docker.com/engine/reference/builder/#volume), you can add geckodriver to your images in Dockerfile like:

    From python
    RUN mkdir -p /usr/src/
    ADD geckodriver_host_path /usr/src/

or create a volume to bind the you directory between container and host:

   docker run -v /geckodriver_host_path:/usr/src/

If you don't do this you may get error message:

    Traceback (most recent call last):
      File "test_sele.py", line 9, in <module>
        browser = webdriver.Firefox()
      File "/usr/local/lib/python3.5/site-packages/selenium/webdriver/firefox/webdriver.py", line 142, in __init__
        self.service.start()
      File "/usr/local/lib/python3.5/site-packages/selenium/webdriver/common/service.py", line 81, in start
        os.path.basename(self.path), self.start_error_message)
    selenium.common.exceptions.WebDriverException: Message: 'geckodriver' executable needs to be in PATH. 
    
    from pyvirtualdisplay import Display
    from selenium import webdriver

    display = Display(visible=0, size=(1024, 800))
    display.start()

    browser = webdriver.Firefox()
    browser.get('http://www.bing.com')
    print(browser.title)
    assert('Bing' in browser.title)
    browser.quit()

    display.stop()

Everything should work now(finally :<), Although you now can run the test, but still hard to debug without GUI, one more tips to get the error message in javascript:

    <script type="text/javascript">
    window.onerror=function(msg){
        $("body").attr("JSError",msg);
    }
    </script>
then in your test.py

    body = self.browser.find_element_by_tag_name('body')
    print(body.get_attribute("JSError")
Now you know the javascript error even if you don't have GUI. 

###Finally, use docker! 

Docker is great, docker images installed all stuff you need and you just need to connetc it then it work. wait, the trick it connecting, if you not careful about the port or the network connecting, it will become devil.

[zalenium](https://github.com/zalando/zalenium)(for video recording) 
[docker-selenium](https://github.com/SeleniumHQ/docker-selenium)

Since zalenium have video recording, so I choose it.I'm using django for my project, and django have a StaticLiveServerTestCase which auto create a another server in port 8081 in default, so I run test like this 

    python manage.py test django_test --liveserver=localhost:8081

django_test like this:

    class BaseTestStaticLiveServerTestCase(StaticLiveServerTestCase):

        @classmethod
        def setUpClass(cls):
            cls.driver = webdriver.Remote(
                command_executor='http://zalenium:4444/wd/hub',
                desired_capabilities=DesiredCapabilities.FIREFOX)
            cls.brower_size = cls.driver.get_window_size()
            cls.live_server_url = 'http://web:8081'
            super(BaseTestStaticLiveServerTestCase, cls).setUpClass()

        @classmethod
        def tearDownClass(cls):
            cls.driver.quit()
            super(BaseTestStaticLiveServerTestCase, cls).tearDownClass()


I create setUpClass and tearDownClass method so django only start the browser one time.
