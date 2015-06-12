### OpenWrt packages feed

This is an OpenWrt package feed containing [HomeWSN](http://homewsn.github.io) related packages.

To use these packages, add the following line to the `feeds.conf.default` in the OpenWrt buildroot:

    src-git homewsn https://github.com/homewsn/homewsn.openwrt.packages.git

This feed should be included and enabled by default in the OpenWrt buildroot. To install all its package definitions, run:

    ./scripts/feeds update homewsn
    ./scripts/feeds install -a -p homewsn

The packages should now appear in menuconfig.

Update the homewsn feed:

    ./scripts/feeds update homewsn
