# PHP - pecl-memcache Windows binaries

- **src**: https://github.com/websupport-sk/pecl-memcache
- **doc:** https://www.php.net/manual/en/book.memcache.php

> Personally use (and working fine...) **x64 avx nts** version.  
> See ```memcache.ini``` configuration file exemple

## `VC15 & VS16`
- VS16 : toolset 14.28.29333
- VC15 : toolset 14.16.27023
  - MSVC redist  [x86](https://aka.ms/vs/16/release/vc_redist.x86.exe) - [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)
- Window Kit 10.0.19041.0
- **[AVX](https://msdn.microsoft.com/fr-fr/library/jj620901.aspx) releases** __for specified directory__

### Version 4.0.5.2 "[e090f05](https://github.com/websupport-sk/pecl-memcache/commit/e090f05a05e4b773f750025a7647f5d590aa4a6a)" branch `NON_BLOCKING_IO_php8`

> 2020-10-16 - commit
>
> 2020-11-14 - build

- [patched](https://github.com/nono303/PHP7-memcache-dll/blob/master/current.patch) with:
  - [PR77](https://github.com/websupport-sk/pecl-memcache/pull/77) Explicitly disconnect from servers
  - [PR78](https://github.com/websupport-sk/pecl-memcache/pull/78) Memory corruption when server unreachable, and `memcache_get()` with multiple keys
  - [39656](https://www.apachelounge.com/viewtopic.php?p=39656#39656) Compatibility fix for PHP 8

- `php-8.0.x_memcache.dll` with [php-src 8.0.0RC4](https://github.com/php/php-src/tree/php-8.0.0RC4)  
- `php-7.4.x_memcache.dll` with [php-src 7.4.12](https://github.com/php/php-src/tree/php-7.4.12)  
- `php-7.3.x_memcache.dll` with [php-src 7.3.24](https://github.com/php/php-src/tree/php-7.3.24)  
- `php-7.2.x_memcache.dll` with [php-src 7.2.34](https://github.com/php/php-src/tree/php-7.2.34)  

### Version 4.0.5.2 "[4fdcbf9](https://github.com/websupport-sk/pecl-memcache/commit/4fdcbf9fdb6876b50cd73c614bf8130ee10ce2d2)" branch `NON_BLOCKING_IO_php7`

> 2020-09-24 - commit
>
> 2020-11-14 - build

- `php-7.1.x_memcache.dll` with [php-src 7.1.33](https://github.com/php/php-src/tree/php-7.1.33) 

##  `VC14` _discontinued_ ![](https://placehold.it/15/f03c15/000000?text=+)

### Version [3.0.9-dev](https://github.com/websupport-sk/pecl-memcache/commit/4991c2fff22d00dc81014cc92d2da7077ef4bc86)

> 2016-12-08

- `php-7.1.x_memcache.dll` with [php-src 7.1.0](https://github.com/php/php-src/tree/php-7.1.0) 
- `php-7.0.x_memcache.dll` with [php-src 7.0.6](https://github.com/php/php-src/tree/php-7.0.6) 

----
#### **Check your version**

- Look for **'Product name'** in detailed information of the dll file in Windows explorer 
  ![image-20201114113957010](README_1.png)

- Look at `php_info()` 
  ![image-20201114113957010](README_2.png)

#### **Build Scripts** 

- [@nono303/win-build-scripts](https://github.com/nono303/win-build-scripts)

#### Dependencies

- [php-sdk-binary-tools 2.2.0](https://github.com/microsoft/php-sdk-binary-tools/tree/php-sdk-2.2.0)
- [php-sdk 'staging'](https://windows.php.net/downloads/php-sdk/deps/series/)

#### CFLAGS add:

- [/GL](https://msdn.microsoft.com/en-us/library/0zza0de8.aspx)
- MD
- /Zi
- /O2

#### LDFLAGS add:

- [/LTCG ](https://msdn.microsoft.com/en-us/library/xbf3tbeh.aspx)
- [/NODEFAULTLIB](https://msdn.microsoft.com/en-us/library/3tz4da4a.aspx):[libcmt.lib ](https://msdn.microsoft.com/en-us/library/abx4dbyh.aspx) /NODEFAULTLIB:MSVCRTD.lib
- [/OPT:ICF](https://msdn.microsoft.com/en-us/library/bxwfs976.aspx)

----
2016-05-18
> I’ve noticed __2 bugs__ when implementing memcache session.handler for 
```
session.save_handler = memcache
session.save_path = "tcp://127.0.0.1:11211"
```
1. With ```memcache.protocol = ascii```, there is some random lock on ```session_start()``` according to ```memcache.lock_timeout```
so i've set ```memcache.lock_timeout = 1``` but that doesn’t resolve the problem (just makes it less visible..)
2. With ```memcache.protocol = binary```, first bug seems not appearing but session destroy failed !
All that test have been done with phpmyadmin which write complex data in session

So you can find ```MemcacheSessionHandlerPrepend.php``` a MemcacheSessionHandler implementing SessionHandlerInterface to add to your ```php.ini``` with config:
```
session.save_handler = user
auto_prepend_file = c:/path/to/MemcacheSessionHandlerPrepend.php
; session.save_path = 
```
_See [issue #23](https://github.com/websupport-sk/pecl-memcache/issues/23#issuecomment-327702906) and [discution](http://stackoverflow.com/questions/34952502/memcache-for-php7-on-windows/) on stackoverflow_
