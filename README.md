To make sure MPP works for Chromium:

  - The dependency `libv4l-rkmpp-git` needs [SoC-specific setup](https://github.com/7Ji-PKGBUILDs/libv4l-rkmpp-git), if not set it won't work. For RK3588(s), running the following command once is enough:
```
sudo systemctl enable --now libv4l-rkmpp-setup.service
```
  - You must run Chromium with Mali blob drivers with EGL for the MPP decoding pipeline to work, e.g. for [libmali of Valhall G610 under X11](https://github.com/7Ji-PKGBUILDs/libmali-valhall-g610-x11-gbm), use the following command:
```
LD_LIBRARY_PATH=/usr/lib/mali-valhall-g610/x11-gbm chromium --use-gl=egl
```

---

To quickly verify if hwvdec works for Chromium, open page `chrome://gpu` and check `Video Acceleration Information`, if you've set up it correctly you should see the table populated with all kinds of codec decoding capabilities.

---

About building issues:

  - Lagging behind the actual Chromium, this package needs extra care when building, as some dependencies might be broken when the system libraries are newer. An example of this is the `re2` pacakge, different from the offiical Chromium pacakge, I had to change this package to use `re2` from Chromium's source tree instead of the system one. Down the road there might be more dependencies broken, and I don't always have time to fix it. 
  - A pre-built pacakge is available from [my repo](https://github.com/7Ji/archrepo), alongside all of dependencies not in the official repos.

---

About update requests and out-of-date flags:

  - Please note this package is based on [the patches released by Rockchip](https://github.com/JeffyCN/meta-rockchip/tree/master/dynamic-layers/recipes-browser/chromium) which is lagged behind Chromium itself, so while this appears outdated it's actually up-to-date.