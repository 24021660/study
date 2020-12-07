# 1.三方登录
代码如下:
```python
from django.http import HttpResponse
from django.shortcuts import render
from django.shortcuts import redirect
from json import dumps

from django.http import HttpResponseRedirect
from urllib.parse import urlencode

steam_openid_url = 'https://steamcommunity.com/openid/login'  #三方登录网址

#首先是触发链接用home表示
def home(request):
    a = '<a href="http://127.0.0.1:8000/auth">Login with steam</a>'
    return HttpResponse(a)


#该网页为向三方登录网站post一组参数,并返回到设定好的的authorize网址中
def auth_with_steam(request):
    params = {
        'openid.ns': "http://specs.openid.net/auth/2.0",
        'openid.identity': "http://specs.openid.net/auth/2.0/identifier_select",
        'openid.claimed_id': "http://specs.openid.net/auth/2.0/identifier_select",
        'openid.mode': 'checkid_setup',
        'openid.return_to': 'http://127.0.0.1:8000/authorize',
        'openid.realm': 'http://127.0.0.1:8000'

    }
    query_string = urlencode(params)
    auth_url = steam_openid_url + '?' + query_string
    print(auth_url)
    # return redirect(auth_url)
    return HttpResponseRedirect(auth_url)

#最后回到该网址
def authorize(request):
    print('========================================================================')
    print(request.body)
    print('========================================================================')
    return HttpResponse('<a href="http://127.0.0.1:8000/auth">Login with steam</a>')
```
# 2.添加好友
代码如下:
先发起打开steam的指令:
然后,添加好友:`steam://friend/add/`
写成js代码:

