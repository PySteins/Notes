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

