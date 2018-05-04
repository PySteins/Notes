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
