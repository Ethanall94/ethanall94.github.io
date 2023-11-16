---
layout: posts
title:  "회원가입 MAIL 인증 구현하기(Google SMTP)"
date:   2023-11-12 12:33:00 +0900
categories: Backend, Django, Feature, Python
---
<aside>
💡 로그인하면 인증메일이 발송되게 해봅시다!

</aside>

### 과정

---

1. `models.py` 에 다음과 같이 작성해 User model을 추가합니다.
    
    ```python
    class User (AbstractBaseUser):
        """
        사용자 정보에 대한 모델 
        """
    
        email                               = models.EmailField(verbose_name = "이메일", max_length = 100, unique = True)
        password                            = models.CharField(verbose_name = "비밀번호")
    
    				⋮
    
    		is_active                           = models.BooleanField(default = False)
    ```
    
2. `mail.json` 을 생성해 다음과 같이 작성합니다.
    
    ```json
    {
        "EMAIL_HOST_USER"      : "sample@gmail.com",
        "EMAIL_HOST_PASSWORD"  : "sample password"
    }
    ```
    
3. `settings.py` 에 다음을 추가해줍니다.
    
    ```python
    # mail 인증
    mail = json.load(open(os.path.join(SECRETS_DIR, 'mail.json')))
    
    EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
    EMAIL_USE_TLS = True
    EMAIL_PORT = 587
    EMAIL_HOST = 'smtp.gmail.com'
    EMAIL_HOST_USER = mail['EMAIL_HOST_USER']
    EMAIL_HOST_PASSWORD = mail['EMAIL_HOST_PASSWORD']
    DEFAULT_FROM_MAIL = EMAIL_HOST_USER
    ```
    
4. `views.py` 에 다음을 추가해줍니다.
    
    ```python
    from django.contrib.sites.shortcuts         import get_current_site
    from django.template.loader                 import render_to_string
    from django.utils.http                      import urlsafe_base64_encode,urlsafe_base64_decode
    from django.core.mail                       import EmailMessage
    from django.utils.encoding                  import force_bytes, force_str
    from .tokens                                import account_activation_toke
    ```
    
    * 로그인 할 때 인증메일 보내주는 함수
    
    ```python
    def signup(request):
        if request.method == 'POST':
            user = User.objects.create_user(email=request.POST['email'], password=request.POST['password'])
            user.is_active = False
            user.save()
            current_site = get_current_site(request) 
            message = render_to_string('account/activation_email.html', {
                'user': user,
                'domain': current_site.domain,
                'uid': urlsafe_base64_encode(force_bytes(user.pk)),
                'token': account_activation_token.make_token(user),
            })
            mail_title = "계정 활성화 확인 이메일"
            mail_to = request.POST["email"]
            email = EmailMessage(mail_title, message, to=[mail_to])
            email.send()
            return redirect("/")
    
        return render(request, 'login.html')
    ```
    
    * 인증 메일 눌렀을 때 인증 확인해서 user database 변경해주는 함수
    
    ```python
    def verify_email(request, uidb64, token):
        try:
            uid = force_str(urlsafe_base64_decode(uidb64))
            user = User.objects.get(pk=uid)
        except(TypeError, ValueError, OverflowError, User.DoesNotExsit):
            user = None
        if user is not None and account_activation_token.check_token(user, token):
            user.is_active = True
            user.save()
            return redirect("/")
        else:
            messages(request, '계정 활성화 오류')
            return render(request, 'index.html')
        return
    ```
    
5. `views.py` 와 같은 경로에 `tokens.py` 파일을 만들어 다음과 같이 추가해줍니다.
    
    ```python
    from django.contrib.auth.tokens import PasswordResetTokenGenerator
    
    class AccountActivationTokenGenerator(PasswordResetTokenGenerator):
        def _make_hash_value(self, user, timestamp):
            return (str(user.pk) + str(timestamp)) +  str(user.is_active)
    
    account_activation_token = AccountActivationTokenGenerator()
    ```
    
6. `app/urls.py` 에 다음과 같이 추가해줍니다.
    
    ```python
    path('signup/', user_views.signup, name='signup'),
    path('activate/<str:uidb64>/<str:token>/', user_views.verify_email, name='activate'),
    ```
    
7. `app/templates/account/activation_email.html` 에 다음과 같이 추가해줍니다.
    {% raw %}
    ```html
    {{user.email}}님, 아래 링크를 클릭하여 계정을 활성화하세요:
     http://{{domain}}{% url 'activate' uid token %}
    ```
    {% endraw %}
    

---