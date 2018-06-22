### django1.4迁移到django1.11注意事项


#### 缓存
  ```python
  from django.core.cache import caches
  cache = caches['myalias']

  # settings file
  CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': '127.0.0.1:11211',
    },
    'redis': {
        "KEY_FUNCTION": make_key,
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "172.16.5.184:6379:1",
        "OPTIONS": {
            'PARSER_CLASS': 'redis.connection.HiredisParser',
            'CONNECTION_POOL_CLASS': 'redis.BlockingConnectionPool',
            'CONNECTION_POOL_CLASS_KWARGS': {
                'max_connections': 50,
                'timeout': 20,
            }
        }
    },
  }
  ```

#### url配置
  ```python
  from django.conf.urls import url, include
  from . import views

  urlpatterns = [
      # 指定同目录下的视图函数
      url('^article/m/(?P<id>\d+)/$', views.mobi_article_detail),

      # 指定其他目录下的urls.py
      url(r'^sem/', include('apps.sem.urls')),
  ]
  ```

#### 库版本问题
  `django-debug-toolbar`需要更新以适配`django1.11`


#### `render_to_response`渲染模板方法更换
  改为
  ```python
  render(request, 'template.html', data)
  ```

#### `template`配置更换
  ```
  TEMPLATE_LOADERS = (
      'django.template.loaders.filesystem.Loader',
      'django.template.loaders.app_directories.Loader',
      'django.template.loaders.eggs.Loader',
  )
  TEMPLATE_CONTEXT_PROCESSORS = (
      'django.core.context_processors.request',
      'django.contrib.auth.context_processors.auth',
      'django.contrib.messages.context_processors.messages',
      #'django.core.context_processors.csrf',
  )

  '''更改为'''
  TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASEDIR, "templates")],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
                ],
        },
    },
]
