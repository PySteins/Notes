1. 浏览器输入注册url`/user/signup`

2. `django`根据url找到视图函数`apps.account.center_views.account_signup` 

3. `account_signup`返回注册页面`templates/account/m_sign-up.html`

4. `<form id="formSignUp ...>`可知表单验证由`static/js/touch/m_sign-up.js`的`formSignUp`完成

5. `formSignUp`提交表单内容后由`handleSignUp`进行下一步操作（跳转至登录页面、密码错误重新输入等）
