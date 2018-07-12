homura_pwn_waf
----
Use patchkit to patch the ELF(Add seccomp rules)
Dependencies
----
- Run `./deps.sh` to automatically install these.
  - Capstone Engine - https://github.com/aquynh/capstone.git
  - Keystone Engine - https://github.com/keystone-engine/keystone.git
  - Unicorn Engine  - https://github.com/unicorn-engine/unicorn.git

----
More details about patchkit : https://github.com/lunixbochs/patchkit

----
该pwn通防的原理是利用Patchkit在程序main函数入口处先跳转执行seccomp禁止一些像execve之类的系统调用，然后跳转会主函数执行原来的程序，可以自定义过滤规则，这里给出两份过滤规则，下面会详细说明。

----
用法
现将hpwnwaf.py 中 main_addr 改为需要修改的二进制文件中main函数入口地址,然后执行```./patch ELF hpwnwaf.py``` 
暂时只支持linux 64位程序

hpwnwaf 中的过滤规则为 
``` 
  scmp_filter_ctx ctx;
  ctx = seccomp_init(SCMP_ACT_ALLOW);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(socket), 0);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(connect), 0);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(bind), 0);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(listen), 0);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(clone), 0);  
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(execve), 0); 
```
hpwnwaf2中的过滤规则为 
```
  scmp_filter_ctx ctx;
  ctx = seccomp_init(SCMP_ACT_ALLOW);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(bind), 0);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(listen), 0);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(execve), 0); 
```
如需要自定义，可以参考我这篇文章:
http://homura.cc/blog/archives/145
