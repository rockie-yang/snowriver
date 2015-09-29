---
ID: 706
post_title: Jython远程调试
author: riv
post_date: 2012-07-22 04:00:21
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2012/07/22/jython%e8%bf%9c%e7%a8%8b%e8%b0%83%e8%af%95/
published: true
---
<!--:en--><img alt="jython" src="http://www.jython.org/css/jython.png" title="jython" class="alignright" width="150" height="90" />It is all empty in the office this days. Most Swedes went for summer vacation. So I am not very busy and can spend some time to play technology.

There are few ways to debug a problem.
<ul>
	<li>print information to debug file, then analyze the debug file. Some problem only can use this way to locate. For example, logic issues will only happen when system running in a high pressure.</li>
	<li>debug trace, check variable values. It is specially useful when doing logic debug. There are few ways to drive debug trace, unit test, remote debug. Unit test is much easier then remote debug, if possible I will unit test but not remote debug.</li>
	<li>dump memory. It is used mostly for memory issue analyzation. </li>
</ul>

Download Juno from eclipse web site<a href="http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/juno/R/eclipse-java-juno-win32.zip">download Juno</a>
I could not manage to install PyDev on line. So use office install instead. <a href="http://sourceforge.net/projects/pydev/files/latest/download?source=files">download pydev</a>。

there are few articles found on google about how to remote debug.
<ul>
	<li>On XBMC web site using XBMC，<a href="http://wiki.xbmc.org/index.php?title=How-to:Debug_Python_Scripts_with_Eclipse">Debug_Python_Scripts_with_Eclipse</a>。</li>
	<li>On python web site using Java Debug Wire Protocol。<http://wiki.python.org/jython/ClarkUpdike/RemoteJythonDebugging>RemoteJythonDebugging</a>。</li>
	<li>On Pydev web site, <a href="http://pydev.org/manual_adv_remote_debugger.html">manual_adv_remote_debugger</a>。</li>
</ul>

I only tried PyDev way. The step is the same as described in the article. I just write small example to drive it and got a small tip in pydev.

The way I wanna try is invoke Jython program from Java. And then I set break point either in java or Jython. A simple JUNIT test case is used to drive it.
<pre class="brush:java">
@Test
public void test() {
    Properties props = new Properties();
    
    // The following information can use simple program
    // run an print it. 
    // print(os.environ['PYTHONPATH'])
    props.setProperty("python.path",
        "C:\\jython2.5.2\\eclipse\\plugins\\org.python.pydev_2.6.0.2012062818\\pysrc");
    PythonInterpreter.initialize(System.getProperties(), props,
                     new String[] {""});
    
    PythonInterpreter interp = new PythonInterpreter();
    interp.execfile(this.getClass().getResourceAsStream("whatever.py"));
    
    // It is just for test after jython execution, 
    // the break point still can back to here
    assertTrue(true);
}
</pre>
<pre class="brush:python">
# debug.py
enabledModules = ['debug', 'whatever']

def remote_debugable_if_enabled(name):    
    import os
    import pydevd

    if name in enabledModules:
        pydevd.settrace(stdoutToServer=True, stderrToServer = True)
    
    return

if __name__ == '__main__':
    remote_debugable_if_enabled('debug');
</pre>

Then add following two lines to every files which possibly need remote debug.
<pre class="brush:python">
import debug
debug.remote_debugable_if_enabled(__name__)
</pre>

Modify debug.py if wanna enable debug, and then that file need be reloaded.

Tip: If PyDev always locate to a wrong file when you want remote debug more then one file. Try this Window->Preference->Pydev->Debug->Source Locator, clear all.<!--:--><!--:zh--><img alt="jython" src="http://www.jython.org/css/jython.png" title="jython" class="alignright" width="100" height="65" />这几天公司没有几个人上班了，全休假去了。事也不忙，有空杂七杂八的看看。玩玩Jython。

当软件出问题时，有几种办法可以查找问题。
<ul>
	<li>打印日志，再分析日志。有些问题只能用这种方法来查找。譬如说只有在高压力下才会出现到奇怪问题。</li>
	<li>单步跟踪，查看变量值。在分析功能性问题的时候，这种方法最管用。而驱动单步可以有多种方式，如单元测试啊，远程调试啊。如果单元测试能够找到的问题尽量用单元测试。远程调试还是比较麻烦。</li>
	<li>dump内存镜像。这一般在找内存问题时候使用</li>
</ul>

从eclipse网站<a href="http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/juno/R/eclipse-java-juno-win32.zip">下载Juno</a>
在线安装PyDev没有成功。<a href="http://sourceforge.net/projects/pydev/files/latest/download?source=files">下载pydev</a>之后解压到eclipse目录下。

网上搜到几篇文档，关于如何远程调试的。
<ul>
	<li>XMBC网站上的，用XBMC的，<a href="http://wiki.xbmc.org/index.php?title=How-to:Debug_Python_Scripts_with_Eclipse">Debug_Python_Scripts_with_Eclipse</a>。</li>
	<li>JSON网站上的，用Java Debug Wire Protocol的。<http://wiki.python.org/jython/ClarkUpdike/RemoteJythonDebugging>RemoteJythonDebugging</a>。</li>
	<li>Pydev网站上的，<a href="http://pydev.org/manual_adv_remote_debugger.html">manual_adv_remote_debugger</a>。</li>
</ul>

我拿Pydev的方式试了一下，步骤和这篇文档描述一致。

从JAVA调用Jython程序。然后单步到Jython程序里。下面是一个简单的JUNIT TestCase驱动。
<pre class="brush:java">
@Test
public void test() {
    Properties props = new Properties();
    
    // 下面这些内容可以写一个简单的jython程序，
    // 运行一下打印出来就可以了
    // print(os.environ['PYTHONPATH'])
    props.setProperty("python.path",
        "C:\\jython2.5.2\\eclipse\\plugins\\org.python.pydev_2.6.0.2012062818\\pysrc");
    PythonInterpreter.initialize(System.getProperties(), props,
                     new String[] {""});
    
    PythonInterpreter interp = new PythonInterpreter();
    interp.execfile(this.getClass().getResourceAsStream("whatever.py"));
    
    // 这一行是为了测试，jython运行完代码后可以在这一行再中断
    assertTrue(true);
}
</pre>
<pre class="brush:python">
enabledModules = ['debug', 'whatever']

def remote_debugable_if_enabled(name):    
    import os
    import pydevd

    if name in enabledModules:
        pydevd.settrace(stdoutToServer=True, stderrToServer = True)
    
    return

if __name__ == '__main__':
    remote_debugable_if_enabled('debug');
</pre>

在每个有可能要单步的文件开头加上这么两句。
<pre class="brush:python">
import debug
debug.remote_debugable_if_enabled(__name__)
</pre>

单步之后Pydev会提示定位源文件。如果要调试某个文件那么修改debug.py，再把要调试的文件重新加载就可以了。

如果单步多个文件有可能老是指向不对的文件。这时可以把配置里把源文件清一下Window->Preference->Pydev->Debug->Source Locator。<!--:-->