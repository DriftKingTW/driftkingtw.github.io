---
title: Using DisplayCAL to Calibrate External Monitor on Apple Silicon Mac
tags:
  - Apple Silicon
  - macOS
  - DisplayCAL
keywords:
  - Apple Silicon
  - Apple
  - M1
  - Mac
  - Color Calibration
  - DisplayCAL
abbrlink: 7505ed62
date: 2022-02-07 19:00:00
---

I've got myself an Apple Silicon Mac last year, although it's really amazing but still have some compatibility issues. When I want to use `DisplayCAL` to recalibrate my external monitor, but the it didn't recognize both of my external monitor.

Here's the workaround for this issue:

![DisplayCAL](https://static.driftking.tw/2024/06/675f6086152e3d04ff0572c2d9968db7.png)

<!-- more -->

# Wrong ArgyllCMS Version

`DisplayCAL` will install `ArgyllCMS` when first launch, but it won't recognize my external monitor:

![DisplayCAL can't detect any external monitor](https://static.driftking.tw/2024/06/fd78c000cd271230e01d9fc52d2ccf05.png)

The reason why is `DisplayCAL` actually not install the latest version of `ArgyllCMS` (though it says it's the latest)

So we need to install the latest version of `ArgyllCMS` by ourselves and replace it (`v2.3.0` when I writing this)

1. Go to [DisplayCAL](https://displaycal.net/) and download the latest version of `DisplayCAL`

2. Plug in your monitor calibrator (`SpyderX` in my case)

3. It will prompt you to download the "latest version" of `ArgyllCMS`

![DisplayCAL installs ArgyllCMS](https://static.driftking.tw/2024/06/4cf7c4696443bfa66f69a0c201a88fad.png)

4. Cancel it and download `Intel OS X 10.6 64 bit or later` from [ArgyllCMS](https://www.argyllcms.com/downloadmac.html)

5. Unzip the `.tgz` archive, and move `Argyll_V2.3.0` folder to `~/Library/Application Support/DisplayCAL/dl` (version might be different)

6. Go back to `DisplayCAL` and choose `File > Locate ArgyllCMS executables...` from menu bar.

7. Set `ArgyllCMS` location to `~/Library/Application Support/DisplayCAL/dl/Argyll_V2.3.0/bin/`

8. The external monitor should appear in list after restarting `DisplayCAL`, macOS might prompt you to prevent from unauthorized app to run,
remember to force open all executables from Security and Privacy settings.

![DisplayCAL detected external monitor](https://static.driftking.tw/2024/06/c4bb12f1160dbd10a20b22dd7fe596c9.png)

{% colorquote warning %}
I've ran into some glitches with this solution, it will turn the monitor black when the calibration is completed at first time, I need to re-plug the monitor cable to lit up again and allow macOS to run the last step to finish install ICC profile. It's better to run a quick calibration at first to see if there's any problem.
{% endcolorquote %}

# Reference

- [Mac Mini M1 color calibration](https://www.reddit.com/r/macmini/comments/kembz2/mac_mini_m1_color_calibration/)
- [DisplayCAL](https://displaycal.net/)
- [ArgyllCMS](https://www.argyllcms.com/)