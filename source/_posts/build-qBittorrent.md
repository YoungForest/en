---
title: Manually Building qBittorrent on Mac
date: 2021-02-27 17:10:01
tags:
- Tinkering
categories:
- tech
---

## Cause

Recently the pressure of graduation has been quite heavy, so I wanted to download a movie from Beiyouren and relax a bit. But I found that qBittorrent, the BitTorrent client I had always used for seeding and downloading, could no longer open. I searched online for quite a while for the cause and solution.

I finally confirmed that the culprit was a Mac update. qBittorrent was treated as an app with incomplete verification: [Issue 11570](https://github.com/qbittorrent/qBittorrent/issues/11570).
There are two solutions:

- Disable Apple's security checks
- Manually build the app yourself

For certain reasons, I could not make too many system-level changes to the Mac. So I had to try the second solution. It turned out that manually building qBittorrent was not simple; one whole afternoon passed like that, and I did not get to watch the movie either. To make it easier for classmates with the same problem to reference, I record my solution here. During my build process, I did not find similar tutorials or references online, and there were indeed quite a few pitfalls.

## Steps

If there is no Qt environment before, `./configure` will report an error that it cannot find `qmake`. The corresponding environment needs to be configured.

```bash
brew install qt
echo 'export PATH="/usr/local/opt/qt/bin:$PATH"' >> ~/.zshrc
export PATH="/usr/local/opt/qt/bin:$PATH"
export LDFLAGS="-L/usr/local/opt/qt/lib"
export CPPFLAGS="-I/usr/local/opt/qt/include"
export PKG_CONFIG_PATH="/usr/local/opt/qt/lib/pkgconfig
```

If there is no `boost` environment before, it will report an error that it cannot find `boostlib`:

```bash
checking for boostlib >= 1.65 (106500)... configure: We could not detect the boost libraries (version 1.65 or higher). If you have a staged boost library (still not installed) please specify $BOOST_ROOT in your environment and do not give a PATH to --with-boost option.  If you are sure you have boost installed, then check your version number looking in <boost/version.hpp>. See http://randspringer.de/boost for more documentation.
configure: error: Could not find Boost
```

The `boost` environment needs to be installed.

```bash
brew install boost
```

```bash
brew install libtorrent-rasterbar
```

If you encounter this error,

```bash
Error: Directory not empty @ dir_s_rmdir - /usr/local/opt/openssl
```

you can delete the corresponding folder or change its owner with `chown`.

```bash
brew cleanup
sudo rm -rf /usr/local/Cellar/openssl/1.0.2q
```

After I ran `brew install libtorrent-rasterbar` and then `./configure`, I still ran into a wrong-version problem for that library:

```bash
Requested 'libtorrent-rasterbar >= 1.2.11' but version of libtorrent-rasterbar is 1.2.10
```

Because the `brew` formula only had 1.2.10, we had to manually build a newer version.

Go to GitHub and find the source code for the corresponding version of libtorrent-rasterbar: [download](https://github.com/arvidn/libtorrent/releases/tag/v1.2.11). Compile and install it according to the build instructions in the documentation.

For Mac:

```bash
brew install boost-build boost openssl@1.1
echo "using darwin ;" >>~/user-config.jam
b2 crypto=openssl cxxstd=17 openssl-lib=/usr/local/Cellar/openssl@1.1/1.1.1j/lib openssl-include=/usr/local/Cellar/openssl@1.1/1.1.1j/include release
b2 crypto=openssl cxxstd=17 openssl-lib=/usr/local/Cellar/openssl@1.1/1.1.1j/lib openssl-include=/usr/local/Cellar/openssl@1.1/1.1.1j/include install --prefix=/usr/local
```


```bash
export libtorrent_LIBS="-L/usr/local/lib"
export libtorrent_CFLAGS="-I/usr/local/include"
export openssl_CFLAGS="-I/usr/local/Cellar/openssl@1.1/1.1.1j/include"
export openssl_LIBS="-L/usr/local/Cellar/openssl@1.1/1.1.1j/lib"
./configure
make && make install
qbittorrent
```

<!-- ```bash
Undefined symbols for architecture x86_64:
  "_EVP_sha512"
``` -->

In addition, you can also refer to [some other official compilation and build documents](https://github.com/qbittorrent/qBittorrent/wiki#compilation). They provide build options using CMake or Qt Creator.
