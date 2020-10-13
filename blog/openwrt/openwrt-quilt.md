# openwrt quilt


## application


#### Adding a kernel patch

```sh
# prepare the kernel tree
make target/linux/{clean,prepare} V=s QUILT=1
# enter the kenel source directory
cd build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/linux-ramips_mt7688/linux-3.18.44/
# check all patches(not mandatory)
quilt series
# apply all patches
quilt push -a
# create a new, empty patch file
quilt new platform/800-driver-staging-fbtft.patch
# do modification
...
# review the modification
quilt diff
# update the patch file with the changes made
quilt refresh
# cgabge bacj ti tge tiokevek durectirt
cd ../../../../

make target/linux/refresh V=s
# move the new patch file over the buildroot
make target/linux/update V=s
# recompile linux
make target/linux/{clean,compile,install} V=s
```
#### Refreshing patches

When a patched package (or kernel) is updated to a newer version, existing patches might not apply cleanly anymore and patch will report fuzz when applying them. To rebase the whole patch series the refresh make target can be used: 

```
make package/example/refresh V=s
```

For kernel, use:

```
make target/linux/refresh V=s
```

#### Iteratively modify patches without cleaning the source tree

When implementing new changes, it is often required to edit patches multiple times. To speed up the process, it is possible to retain the prepared source tree between edit operations.

```
    Initially prepare the source tree as documented above
    Change to the prepared source directory
    Advance to the patch needing changes
    Edit the files and refresh the patch
    Fully apply the remaining patches using quilt push -a (if any)
    From the toplevel directory, run make package/example/{compile,install} or make target/linux/{compile,install} for kernels
    Test the binary
    If further changes are needed, repeat from step 2.
    Finally run make package/example/update or make target/linux/update for kernels to copy the changes back to buildroot
```

## Naming patches

vaild for target/linux/generic and <arch>:

```
The patches-* subdirectories contain the kernel patches applied for every
OpenWrt target. All patches should be named 'NNN-lowercase_shortname.patch'
and sorted into the following categories:

0xx - upstream backports
1xx - code awaiting upstream merge
2xx - kernel build / config / header patches
3xx - architecture specific patches
4xx - mtd related patches (subsystem and drivers)
5xx - filesystem related patches
6xx - generic network patches
7xx - network / phy driver patches
8xx - other drivers
9xx - uncategorized other patches

ALL patches must be in a way that they are potentially upstreamable, meaning:

- they must contain a proper subject
- they must contain a proper commit message explaining what they change
- they must contain a valid Signed-off-by line
```

## reference

<https://oldwiki.archive.openwrt.org/doc/devel/patches>

<http://chenjingsi.com/programming/openwrt/openwrt-00006.html#sec-5>
