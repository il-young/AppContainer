# AppContainer
Embed an exe application into the panel control. make it feels like you own this exe application.
http://www.cnblogs.com/bitzhuwei/archive/2012/05/24/SmileWei_EmbeddedApp.html
<p>최근진행했던 프로젝트에서언급한요구사항인데, 자체개발폼에기존폼응용인터페이스를삽빩입 (실제로는...자체개발폼과같습니다.) 아직은약의차이가있는임베디드프로그램과호스트프로그램의창활성화 상태입니다.)</p>
<p>在codeproject找到了一篇相关的文章（<a title="Hosting EXE Applications in a WinForm project" href="http://www.codeproject.com/Articles/9123/Hosting-EXE-Applications-in-a-WinForm-project" target="_blank">http://www.codeproject.com/Articles/9123/Hosting-EXE-Applications-in-a-WinForm-project</a>），다시디자인했습니다 .  먼저 그림바로위:</p>
<p>直接上图先：</p>
<p>&nbsp;<a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205241831233469.png"><img style="display: inline; border-width: 0px;" title="嵌入QQ影音" src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205241831247863.png" alt="嵌入QQ影音" width="365" height="254" border="0" /></a> <a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205241831258877.png"><img style="display: inline; border-width: 0px;" title="嵌入Windows Live Writer" src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205241831258287.png" alt="嵌入Windows Live Writer" width="327" height="254" border="0" /></a></p>
<p><a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/20120524183127630.png"><img style="display: inline; border-width: 0px;" title="嵌入photoshop" src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205241831279169.png" alt="嵌入photoshop" width="389" height="254" border="0" /></a> <a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205241831289103.png"><img style="display: inline; border-width: 0px;" title="嵌入Adobe Reader" src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205241831288513.png" alt="嵌入Adobe Reader" width="355" height="254" border="0" /></a></p>
<p>Adobe Reader를 실행한 사진을 보면 소위 "임베디드 프로그램 및 호스트 프로그램의 창 활성화 상태 문제"를 확인할 수 있습니다. 포함된 프로그램 창이 활성화되면 표면적으로 이를 감싸는 호스트 창이 비활성화됩니다. 이를 숨기려면 창의 FormBorderStyle 속성을 없음으로 설정한 다음 창에 닫기, 최대화 및 최소화 버튼을 직접 그립니다.</p>
<p>&nbsp;</p>
<p>원저자의 구현 아이디어가 본질을 더 잘 드러낼 수 있으므로 여기서는 원저자의 코드 조각을 사용하여 구현 과정을 설명합니다.</p>
<p>1. 임베디드 애플리케이션 프로세스를 시작합니다.</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;"> 1</span> Process p = <span style="color: #0000ff;">null</span><span style="color: #000000;">; 
</span><span style="color: #008080;"> 2</span> <span style="color: #0000ff;">try</span> 
<span style="color: #008080;"> 3</span> <span style="color: #000000;">{
</span><span style="color: #008080;"> 4</span>   <span style="color: #008000;">//</span><span style="color: #008000;"> Start the process </span>
<span style="color: #008080;"> 5</span>   p = System.Diagnostics.Process.Start(<span style="color: #0000ff;">this</span><span style="color: #000000;">.exeName); 
</span><span style="color: #008080;"> 6</span> 
<span style="color: #008080;"> 7</span>   <span style="color: #008000;">//</span><span style="color: #008000;"> Wait for process to be created and enter idle condition </span>
<span style="color: #008080;"> 8</span> <span style="color: #000000;">  p.WaitForInputIdle(); 
</span><span style="color: #008080;"> 9</span> 
<span style="color: #008080;">10</span>   <span style="color: #008000;">//</span><span style="color: #008000;"> Get the main handle</span>
<span style="color: #008080;">11</span>   appWin =<span style="color: #000000;"> p.MainWindowHandle; 
</span><span style="color: #008080;">12</span> <span style="color: #000000;">} 
</span><span style="color: #008080;">13</span> <span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception ex) 
</span><span style="color: #008080;">14</span> <span style="color: #000000;">{ 
</span><span style="color: #008080;">15</span>   MessageBox.Show(<span style="color: #0000ff;">this</span>, ex.Message, <span style="color: #800000;">"</span><span style="color: #800000;">Error</span><span style="color: #800000;">"</span><span style="color: #000000;">); 
</span><span style="color: #008080;">16</span> }</pre>
</div>
<p>2. Windows API를 호출하여 시작된 애플리케이션 창을 사용자 정의 컨트롤에 포함합니다(작성자는 Panel 컨트롤을 사용함).</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;">1</span> <span style="color: #008000;">//</span><span style="color: #008000;"> Put it into this form</span>
<span style="color: #008080;">2</span> SetParent(appWin, <span style="color: #0000ff;">this</span><span style="color: #000000;">.Handle);//<span style="color: #008000;">this在这里是Panel控件<br /><br /></span>
</span><span style="color: #008080;">3</span> 
<span style="color: #008080;">4</span> <span style="color: #008000;">//</span><span style="color: #008000;"> Remove border and whatnot</span>
<span style="color: #008080;">5</span> <span style="color: #000000;">SetWindowLong(appWin, GWL_STYLE, WS_VISIBLE);
</span><span style="color: #008080;">6</span> 
<span style="color: #008080;">7</span> <span style="color: #008000;">//</span><span style="color: #008000;"> Move the window to overlay it on this window</span>
<span style="color: #008080;">8</span> MoveWindow(appWin, <span style="color: #800080;">0</span>, <span style="color: #800080;">0</span>, <span style="color: #0000ff;">this</span>.Width, <span style="color: #0000ff;">this</span>.Height, <span style="color: #0000ff;">true</span>);</pre>
</div>
<p>3. 호스트 양식과 함께 변경할 포함된 양식의 크기를 설정합니다.</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;">1</span> <span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">override</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> OnResize(EventArgs e)
</span><span style="color: #008080;">2</span> <span style="color: #000000;">{
</span><span style="color: #008080;">3</span>   <span style="color: #0000ff;">if</span> (<span style="color: #0000ff;">this</span>.appWin !=<span style="color: #000000;"> IntPtr.Zero)
</span><span style="color: #008080;">4</span> <span style="color: #000000;">  {
</span><span style="color: #008080;">5</span>     MoveWindow(appWin, <span style="color: #800080;">0</span>, <span style="color: #800080;">0</span>, <span style="color: #0000ff;">this</span>.Width, <span style="color: #0000ff;">this</span>.Height, <span style="color: #0000ff;">true</span><span style="color: #000000;">);
</span><span style="color: #008080;">6</span> <span style="color: #000000;">  }
</span><span style="color: #008080;">7</span>   <span style="color: #0000ff;">base</span><span style="color: #000000;">.OnResize (e);
</span><span style="color: #008080;">8</span> }</pre>
</div>
<p>4. 호스트 프로그램이 닫힐 때 포함된 양식 응용 프로그램이 닫히도록 설정합니다.</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;"> 1</span> <span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">override</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> OnHandleDestroyed(EventArgs e)
</span><span style="color: #008080;"> 2</span> <span style="color: #000000;">{
</span><span style="color: #008080;"> 3</span>   <span style="color: #008000;">//</span><span style="color: #008000;"> Stop the application</span>
<span style="color: #008080;"> 4</span>   <span style="color: #0000ff;">if</span> (appWin !=<span style="color: #000000;"> IntPtr.Zero)
</span><span style="color: #008080;"> 5</span> <span style="color: #000000;">  {
</span><span style="color: #008080;"> 6</span>     <span style="color: #008000;">//</span><span style="color: #008000;"> Post a colse message</span>
<span style="color: #008080;"> 7</span>     PostMessage(appWin, WM_CLOSE, <span style="color: #800080;">0</span>, <span style="color: #800080;">0</span><span style="color: #000000;">);
</span><span style="color: #008080;"> 8</span> 
<span style="color: #008080;"> 9</span>     <span style="color: #008000;">//</span><span style="color: #008000;"> Delay for it to get the message</span>
<span style="color: #008080;">10</span>     System.Threading.Thread.Sleep(<span style="color: #800080;">1000</span><span style="color: #000000;">);
</span><span style="color: #008080;">11</span> 
<span style="color: #008080;">12</span>     <span style="color: #008000;">//</span><span style="color: #008000;"> Clear internal handle</span>
<span style="color: #008080;">13</span>     appWin =<span style="color: #000000;"> IntPtr.Zero;
</span><span style="color: #008080;">14</span> <span style="color: #000000;">  }
</span><span style="color: #008080;">15</span>   <span style="color: #0000ff;">base</span><span style="color: #000000;">.OnHandleDestroyed (e);
</span><span style="color: #008080;">16</span> }</pre>
</div>
<p>&nbsp;</p>
<p>원저자의 코드는 실제로 사용하기에는 매우 불편하므로, 직접 해보면 자세한 내용을 알 수 있으므로 자세한 내용은 다루지 않겠습니다(아무튼 저는 위의 단계만 배웠고 그의 라이브러리는 사용하지 않았습니다).</p>
<p>저는 사용이 매우 간단하고 세 단계만 거치는 비교적 실용적인 컨트롤을 개발했습니다.</p>
<p>먼저 양식 애플리케이션 프로젝트에서 SmileWei.EmbeddedApp 클래스 라이브러리를 참조합니다.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205241831286528.png"><img style="display: inline; border-width: 0px;" title="프로젝트에서 SmileWei.EmbeddedApp 클래스 라이브러리를 참조합니다." src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205241831298447.png" alt="프로젝트에서 SmileWei.EmbeddedApp 클래스 라이브러리를 참조합니다." width="218" height="244" border="0" /></a></p>
<p>그런 다음 호스트 폼에서 AppContainer 컨트롤을 끌어서 적절한 위치에 배치합니다. (도구 상자에 AppContainer가 없으면 F6을 사용하여 솔루션을 생성한 다음 살펴보세요.)</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025245790.png"><img style="display: inline; border-width: 0px;" title="AppContainer 컨트롤을 끌어서 적절한 위치에 배치합니다." src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/20120524202527717.png" alt="AppContainer 컨트롤을 끌어서 적절한 위치에 배치합니다." width="696" height="424" border="0" /></a></p>
<p>마지막으로 AppContainer 컨트롤에 포함할 애플리케이션(*.exe 파일)의 절대 경로를 알려주고(예를 들어 OpenFileDialog를 사용합니다) AppContainer 컨트롤에 이를 시작하도록 명령합니다.</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;">1</span> appContainer1.AppFilename =<span style="color: #000000;"> openEXE.FileName;
</span><span style="color: #008080;">2</span> appContainer1.Start();</pre>
</div>
<p>이 AppContainer 컨트롤의 이점은 무엇입니까?</p>
<p>1. 원저작자가 생각했던 호스트 프로그램 종료시 크기 조정 및 종료 문제가 AppContainer에 의해 실현되었습니다.</p>
<p>2. AppContainer는 임베디드 애플리케이션이 시작과 분리되도록 지정하여 더 유연하며 개발 과정에서 다음과 같은 상황을 볼 수 없습니다: 원저작자의 제어는 개발 중에 임베디드 프로그램을 로드하는 데 "도움을 줄 수 없습니다".</p>
<p>&nbsp;<a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025297422.png"><img style="display: inline; border-width: 0px;" title="开发的时候原作者的控件就&ldquo;情不自禁&rdquo;地把内嵌程序加载进来了" src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025308751.png" alt="开发的时候原作者的控件就&ldquo;情不自禁&rdquo;地把内嵌程序加载进来了" width="862" height="619" border="0" /></a></p>
<p>3. AppContainer는 자신의 삽입을 금지(무한 루프)하고, 콘솔 프로그램 삽입 시 삽입할 수 없다는 메시지를 표시하고, 매개변수가 null이거나 유효하지 않은지 테스트하는 등 발생할 수 있는 다양한 오류 상황을 방지합니다</p>
<p>4. 기타. 예를들어, AppContainer는 프로그램이올바르게삽입하여 확인하기를 위해Thread.Sleep(1000)과 동일한저급장을사 Ilde를 통해 존재합니다</p>
<p>물론일부애플리케이션은이러한자동화된 방식으로내장될 수 없습니다 .</p>
<p>이 문제를 해결하기 위해호스트 양식의 상태표시에 "Handle Embed" 라벨을 설정했습니다."Handle Embed"를 클릭하면 포함하려는 애플리케이션의 기본 양식 핸들을 입력한 다음 호스트를 입력할 수 있습니다. 양식에 삽입할 수 있습니다.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025326045.png"><img style="display: inline; border-width: 0px;" title=""Handle Embed"를 클릭하면 포함하려는 애플리케이션의 기본 양식 핸들을 입력한 다음 호스트를 입력할 수 있습니다. 양식에 삽입할 수 있습니다. ." src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025344974.png" alt=""Handle Embed"를 클릭하면 포함하려는 애플리케이션의 기본 양식 핸들을 입력한 다음 호스트를 입력할 수 있습니다. 양식에 삽입할 수 있습니다. ." width="420" height="373" border="0" /></a></p>
<p>그런 다음 일부 학생들은 묻습니다. 얼마나 많은 창 핸들을 포함하고 싶은지 어떻게 알 수 있나요? 방법은 여러 가지가 있습니다. 여기서 제가 만든 작은 프로그램도 제공합니다. 여기에서 다운로드할 수 있습니다:<a title="WindowDetective(窗口侦探)0.20.rar" href="http://files.cnblogs.com/bitzhuwei/WindowDetective(%E7%AA%97%E5%8F%A3%E4%BE%A6%E6%8E%A2)0.20.rar" target="_blank">WindowDetective(窗口侦探)0.20.rar</a></p>
<p>인터페이스는 다음과 같습니다.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025352367.png"><img style="display: inline; border-width: 0px;" title="WindowDetective 인터페이스는 다음과 같습니다." src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025362584.png" alt="WindowDetective 인터페이스는 다음과 같습니다." width="304" height="164" border="0" /></a></p>
<p>"Handle: {1903014}" 줄은 내가 사용하고 있는 Windows Live Writer의 기본 양식 핸들을 제공합니다.</p>
<p>사용법은 매우 간단하며, 이 프로그램을 시작하면 자동으로 마우스 위치의 폼 정보를 감지하여 창에 표시해 줍니다. 따라서 알고 싶은 양식의 메뉴 표시줄에 마우스를 올려 놓기만 하면 됩니다. QQ TM 버전도 이런 방식으로 삽입할 수 있습니다. (QQ는 내장할 수 없습니다. Tencent가 무엇을 하는지 모르겠습니다)</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025369170.png"><img style="display: inline; border-width: 0px;" title="QQ TM버전도 이렇게 삽일 할 수 있습니다." src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/20120524202537533.png" alt="QQ TM버전도 이렇게 삽일 할 수 있습니다." width="417" height="586" border="0" /></a></p>
<p>QQ 대화 상자를 삽입해 볼 수도 있습니다. 재미있어요~</p>
<p>&nbsp;<a href="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025372419.png"><img style="display: inline; border-width: 0px;" title="QQ 대화 상자를 삽입해 볼 수도 있습니다. 재미있어요~" src="http://images.cnblogs.com/cnblogs_com/bitzhuwei/201205/201205242025384545.png" alt="QQ 대화 상자를 삽입해 볼 수도 있습니다. 재미있어요~" width="521" height="541" border="0" /></a></p>
<p>&nbsp;</p>
