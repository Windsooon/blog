---
title: How to use selenium when using docker container
date: 2017-06-14 20:39:00
tags: selenium django docker
---

### TL;DR

Use [zalenium](https://github.com/zalando/zalenium)(for video recording) or [docker-selenium](https://github.com/SeleniumHQ/docker-selenium), follow the docs, pay attantion to

- container port connection
- set liveserver if you are using django 
- --network set to your host network
run the command

        docker run --network your_container_network --rm -ti --name zalenium -p 4444:4444 -p 5555:5555 \
        -v /var/run/docker.sock:/var/run/docker.sock \
        -v /tmp/videos:/home/seluser/videos \
        --privileged dosel/zalenium start

run code like this (**web** is your django container id, or container alias in docker-compose)

	python manage.py test django_test --liveserver=web:8081

your django_test.py look like this

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
And everything is done.
    
### Long version: 

Althought my project use docker, but at first I'm not use [zalenium](https://github.com/zalando/zalenium) or [docker-selenium](https://github.com/SeleniumHQ/docker-selenium), and just try to follow the [Selenium docs](https://selenium-python.readthedocs.io/getting-started.html) do it by myself.

first just test selenium in laptop(mac osX), create a virtual environment

    virtualenv -p python3 selenium_test 
active it

    cd selenium_test
    source bin/activate
create a python file for test

    touch test_for_fun.py
in test\_for\_fun.py
    
    from selenium import webdriver
    
    driver = webdriver.Firefox()
    driver.get("http://www.python.org")
    print(driver.title)
    assert("Python" in driver.title)

run it

    python test_for_fun.py
    
If your laptop have GUI and already install Firefox, everything should work well, the script auto open the Firefox browser then done. If you are not using Firefox, you may use [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/getting-started) instead. 

### Now I try to config it with docker, I using Debian as my server. Things become intersting.

![fun](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/program_work.jpg)

Before spend dozen of time to debug this test, first you should make sure do you really need a real browser to run the test, actually, you have some options.

- Use [PhantomJS](http://phantomjs.org/) 
- Use [Headless Chromium](https://developers.google.com/web/updates/2017/04/headless-chrome) 
- Use [Xvfb](https://en.wikipedia.org/wiki/Xvfb)

I read some article about Phantom and Xvfb, like [this](https://news.ycombinator.com/item?id=8418071) and [this](https://watirmelon.blog/2015/12/08/real-vs-headless-browsers-for-automated-acceptance-tests/)

>the problem with Phantom.js is that compared to an actual browser it still falls short.
>Headless browsers make it harder to write/debug tests.

Hard to debug? If you wannt test a complicate project. I guess you the third one is better. Thanks to the great job from the awesome programmers, we don't have to do much but some basic setup. First you have to add this to the /etc/apt/source.list


    deb http://mozilla.debian.net/ jessie-backports firefox-release

then run 
    
    cd ~/ 
    wget mozilla.debian.net/pkg-mozilla-archive-keyring_1.1_all.deb 
    dpkg -i pkg-mozilla-archive-keyring_1.1_all.deb
after that 
    
    #install Xvfb and firefox
    apt-get update && apt-get install -y xvfb -t jessie-backports firefox

After that you should download [geckodriver](https://github.com/mozilla/geckodriver/releases) or [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/downloads) that match your docker container version then add them to your container and specify its path like

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
   
Or you will get error message like this

    Traceback (most recent call last):
      File "test_sele.py", line 9, in <module>
        browser = webdriver.Firefox()
      File "/usr/local/lib/python3.5/site-packages/selenium/webdriver/firefox/webdriver.py", line 142, in __init__
        self.service.start()
      File "/usr/local/lib/python3.5/site-packages/selenium/webdriver/common/service.py", line 81, in start
        os.path.basename(self.path), self.start_error_message)
    selenium.common.exceptions.WebDriverException: Message: 'geckodriver' executable needs to be in PATH. 
    
After that, If you are using python as I do, you can install [PyVirtualDisplay](https://pypi.python.org/pypi/PyVirtualDisplay), If you are not using python, you can try this solution by [A.J](https://stackoverflow.com/questions/6183276/how-do-i-run-selenium-in-xvfb), now your code should look like this:
    
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

Everything should work now(finally :<), Although you now can run the test, but still hard to debug without GUI, this is tips to get the error message in javascript, add 

    <script type="text/javascript">
    window.onerror=function(msg){
        $("body").attr("JSError",msg);
    }
    </script>
to your html file, then in your test.py

    body = self.browser.find_element_by_tag_name('body')
    print(body.get_attribute("JSError")
    
Now you know the javascript error when you run the test even if you don't have GUI. 

### Finally, use docker remote webdriver! 

Docker is great, docker images installed all stuff you need and you just need to connetc it then it work. Wait, if you not care about the port or the network connecting, it will become devil.You may have a look at 

[zalenium](https://github.com/zalando/zalenium)(video recording) 

[docker-selenium](https://github.com/SeleniumHQ/docker-selenium)

Since zalenium have video recording, so I choose it. you should add --network in the docker run command, you can find the network by

    docker inspect your_container_id | grep -i -B 5 IPAMConfig

then

    docker run --rm -ti --name zalenium --network=unicooo_default -p 4444:4444 -p 5555:5555     -v /var/run/docker.sock:/var/run/docker.sock  -e DOCKER=1.11 -v /tmp/videos:/Users/windson/learn/video  dosel/zalenium start


I'm using django for my project, and django have a StaticLiveServerTestCase which create a another server in port 8081 in default, so I run test like this 

    python manage.py test django_test --liveserver=localhost:8081

in django_test.py:

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

Happy coding!
