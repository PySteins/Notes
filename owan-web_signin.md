1. 浏览器输入url`user/signin` 

2. `django`根据url找到视图函数`apps.account.center_views.account_signin`
    ```python
    # apps/acount/urls.py
    url(r'signin/$', center_views.account_signin)
    ```

3. `account`通过`is_user_login`判断当前是否登录，若登录则重定向至`user/home`, 否则返回模板`apps.account/m_sign-in.html`

4. `m_signin.html`通过`<form id="formSignIn" ...>`将表单数据传至`static/js/touch/m_sign-in.js`
    ```javascript
    $('#formSignIn').on('submit', function(event){

        // 确认已输入帐号
        if(!$('[name="username"]').val()){
        ou.dialogMes('请输入帐号');
         
        // 确认已输入密码
        }else if(!$('[name="password"]').val()) {
        ou.dialogMes('请输入密码');
         
        // 是否需要验证码
        }else if (!GLOBAL.DATA.LOGIN.isNeedCaptcha){
        signIn();
         
        // 确认是否已输入验证码
        }else if(!$('[name="captcha"]').val()){
        ou.dialogMes('请输入验证码');
         
        // 信息完整则登录
        }else {
        signIn();
        }
         
        event.preventDefault();
    });
    ```

5. `formSignIn`获取表单数据后转至`signIn()`进行登录操作
    ```javascript
    function signIn() {
        var signInUrl = $('#formSignIn').attr('action')
        var promise = $.ajax({
                        type: "POST",
                        url: signInUrl,
                        data: {
                            username    : $('[name="username"]').val(),
                            password    : $('[name="password"]').val(),
                            key         : GLOBAL.DATA.LOGIN.key,
                            captcha     : $('[name="captcha"]').val(),
                            host_type   : 2
                        },
                        xhrFields: {
                            withCredentials: true
                        }
                    });
        // 成功获取登录返回数据
        promise.done(function(data){
            handleSignIn(data);
        });

        // 登录失败
        promise.fail(function(data){
            ou.dialogMes('网络错误, 请刷新页面重试');
        });
    ```

6. `signIn()`通过ajax方法实现form表单的提交至`/api/user/signin`,交由视图函数`apps.api.account_views.account_signin`
    ```python
    def account_signin(request):
    """login api"""
    # 检查是否需要允许跨域头
    origin_host = request.META.get('HTTP_ORIGIN')
    cors = HostCors(origin_host)

    data = request.POST
    username = data.get('username', None)
    # 获取api返回的验证用户结果
    res = check_account_valid(request, data)
    c, m, uid = res['c'], res['m'], res['uid']
    need_captcha = False
    if c == 10001:
        return cors.result_response(c, m, return_data={'need_captcha': True})

    if c != 0:
        try:
            request.session['login_error_num'] += 1
        except KeyError:
            request.session['login_error_num'] = 0
        login_error_num = request.session.get('login_error_num', 0)
        if login_error_num >= ERROR_NUM_FOR_CAPTCHA:
            need_captcha = True
    else:
        set_login_session(request, uid, username)
        request.session['login_error_num'] = 0

    return cors.result_response(c, m, return_data={'need_captcha': need_captcha})
    ```

8. `account_signin`用户信息验证成功则通过`set_login_session`在`request.session`用户信息
    ```python
    def set_login_session(request, uid, username):
    """设置用户登录session"""
    set_local_sessionid(request, uid, username)

    def set_local_sessionid(request, uid, username):
    """设置本地session"""
    request.session[LOGIN_USER_NAME] = username
    request.session[LOGIN_USER_ID] = uid
    ```

8. `signIn()`登录成功则通过`handleSignIn`跳转至`user/home`或者起源页面，否则按错误进行提示
    ```JavaScript
    function handleSignIn(data) {

        switch(data.c) {


            // 返回0时代表登录成功
            // 页面跳转到个人中心 或者起源页面
            case 0 :
                var redirectURL = ou.getUrlParameter('redirectURL');
                if(redirectURL) {
                    location.href = decodeURIComponent(redirectURL);
                } else {
                    location.pathname = '/user/home/';
                }
                break;


            // 验证码错误
            // 提示验证码错误, 并且显示验证码输入框
            case 10001 :
                ou.dialogMes('验证码错误');
                $('#captchaWrap').show();
                GLOBAL.DATA.LOGIN.isNeedCaptcha = true;
                getCaptcha();
                break;
    ......
    ```

9. 步骤3和6跳转至`/user/home`时会进行是否登录的验证，若验证为未登录则跳转会`user/signin`
    ```python
    def account_home_messages(request):
    """ 系统消息 """
    if not is_user_login(request):
        return HttpResponseRedirect('/user/signin/')
    ......
    ```

10. `is_user_login(request)`通过`request.session`是否存在用户信息判断是否登录
    ```python
    def is_user_login(request):
    """has user login"""
    login_user_name = request.session.get(LOGIN_USER_NAME, None)
    login_user_id = request.session.get(LOGIN_USER_ID, None)
    if login_user_id and login_user_name:
        return True

    return False
    ```
