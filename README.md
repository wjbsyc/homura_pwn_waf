homura_pwn_waf
----
Use elf_patcher to patch the ELF(Add seccomp rules)

Dependencies
----
- Run `./elf_patcher/install_deps.sh` to automatically install these.
  - Capstone Engine - https://github.com/aquynh/capstone.git
  - Keystone Engine - https://github.com/keystone-engine/keystone.git

- More details about elf_patcher : https://github.com/wjbsyc/elf_patcher

- 由于patchkit年久失修, 本项目改为基于elf_patcher实现
- 该pwn通防的原理是利用elf_patcher在程序main函数入口处先跳转执行seccomp禁止一些像execve之类的系统调用，然后跳转会主函数执行原来的程序，可以自定义过滤规则，这里给出两份过滤规则，下面会详细说明。


How to use:
----
- 参照[elf_patcher](https://github.com/wjbsyc/elf_patcher)的说明，执行```./elf_patcher -o OFFSET -A hpwnwaf.asm -O OUTPUT ./ELF``` 


- hpwnwaf 中的过滤规则为 
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
- hpwnwaf2中的过滤规则为 
```
  scmp_filter_ctx ctx;
  ctx = seccomp_init(SCMP_ACT_ALLOW);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(bind), 0);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(listen), 0);
  seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(execve), 0); 
```
如需要自定义，可以参考我这篇文章:
https://blog.homura.cc/articles/2018/07/12/post_02.html
