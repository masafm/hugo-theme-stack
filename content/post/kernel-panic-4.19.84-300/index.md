---
title: "Kernel panic from 4.19.84-300.el7.x86_64"
description: 
date: 2021-02-17T10:17:25+09:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Linux
tags:
    - kernel
---
AHV用にACS overrideパッチを当てたkernelビルドしたらpanicした。
ソースを見たがよくわからないので放置。
パッチは無関係で暗号化のセルフテスト周りでpanicしているようである。
FIPSをオフにすればこのセルフテストは動かなくなる。
```text
[    1.346310] alg: self-tests for sha1-ssse3 (sha1) passed
[    1.346392] alg: self-tests for sha1-ni (sha1) passed
[    1.349783] alg: self-tests for sha256-ssse3 (sha256) passed
[    1.349884] alg: self-tests for sha224-ssse3 (sha224) passed
[    1.350011] alg: self-tests for sha256-ni (sha256) passed
[    1.350058] alg: self-tests for sha224-ni (sha224) passed
[    1.357928] alg: self-tests for sha512-generic (sha512) passed
[    1.357997] alg: self-tests for sha384-generic (sha384) passed
[    1.360691] alg: self-tests for sha512-ssse3 (sha512) passed
[    1.360762] alg: self-tests for sha384-ssse3 (sha384) passed
[    1.374665] CPU feature 'AVX registers' is not supported.
[    1.410444] alg: self-tests for sha1 (sha1) passed
[    1.410566] alg: self-tests for ecb(des3_ede) (ecb(des3_ede)) passed
[    1.410687] alg: self-tests for cbc(des3_ede) (cbc(des3_ede)) passed
[    1.410838] alg: self-tests for ctr(des3_ede) (ctr(des3_ede)) passed
[    1.410861] alg: self-tests for sha256 (sha256) passed
[    1.410897] alg: self-tests for ecb(aes) (ecb(aes)) passed
[    1.410935] alg: self-tests for cbc(aes) (cbc(aes)) passed
[    1.410973] alg: self-tests for xts(aes) (xts(aes)) passed
[    1.411013] alg: self-tests for ctr(aes) (ctr(aes)) passed
[    1.413166] alg: self-tests for rfc3686(ctr-aes-aesni) (rfc3686(ctr(aes))) passed
[    1.413230] alg: self-tests for rfc3686(ctr(aes)) (rfc3686(ctr(aes))) passed
[    1.415995] alg: skcipher: Failed to load transform for cfb(aes): -2
[    1.415999] Kernel panic - not syncing: cfb(aes): cfb(aes) alg self test failed in fips mode!
[    1.416347] CPU: 1 PID: 407 Comm: modprobe Not tainted 4.19.84-300.el7.x86_64 #1
[    1.416577] Hardware name: Nutanix AHV, BIOS 1.11.0-2.el7 04/01/2014
[    1.416776] Call Trace:
[    1.416934]  dump_stack+0x64/0x83
[    1.417087]  panic+0xe8/0x25c
[    1.417250]  alg_test.part.14+0x360/0x380
[    1.417414]  do_test+0x4e74/0x5eed [tcrypt]
[    1.417595]  do_test+0x5edd/0x5eed [tcrypt]
[    1.417782]  ? 0xffffffffc026a000
[    1.417949]  tcrypt_mod_init+0x50/0x1000 [tcrypt]
[    1.418079]  ? 0xffffffffc026a000
[    1.418216]  do_one_initcall+0x4e/0x1d4
[    1.418379]  ? free_unref_page_commit+0x85/0xf0
[    1.418560]  ? _cond_resched+0x15/0x30
[    1.418878]  ? kmem_cache_alloc_trace+0x17f/0x1e0
[    1.419224]  do_init_module+0x5a/0x210
[    1.419464]  load_module.isra.71+0x20cb/0x27e0
[    1.419704]  ? m_show+0x1c0/0x1c0
[    1.419964]  ? vmap_page_range_noflush+0x282/0x420
[    1.420286]  __do_sys_init_module+0x11c/0x180
[    1.420575]  do_syscall_64+0x5b/0x1a0
[    1.420834]  entry_SYSCALL_64_after_hwframe+0x44/0xa9
[    1.421277] RIP: 0033:0x7faeb2b42fca
[    1.421516] Code: 48 8b 0d a9 7e 2c 00 f7 d8 64 89 01 48 83 c8 ff c3 66 2e 0f 1f 84 00 00 00 00 00 0f 1f 44 00 00 49 89 ca b8 af 00 00 00 0f 05 <48> 3d 01 f0 ff ff 73 01 c3 48 8b 0d 76 7e 2c 00 f7 d8 64 89 01 48
[    1.422164] RSP: 002b:00007fff584491d8 EFLAGS: 00000206 ORIG_RAX: 00000000000000af
[    1.422555] RAX: ffffffffffffffda RBX: 0000000000a903d0 RCX: 00007faeb2b42fca
[    1.422828] RDX: 000000000041a96e RSI: 000000000001f05b RDI: 0000000000a9f350
[    1.423099] RBP: 000000000041a96e R08: 0000000000000000 R09: 0000000000a93ee0
[    1.423449] R10: 0000000000a93e00 R11: 0000000000000206 R12: 0000000000a9f350
[    1.423722] R13: 0000000000a905a0 R14: 0000000000040000 R15: 0000000000000000
[    1.424063] Kernel Offset: 0x2d000000 from 0xffffffff81000000 (relocation range: 0xffffffff80000000-0xffffffffbfffffff)
[    1.424682] ---[ end Kernel panic - not syncing: cfb(aes): cfb(aes) alg self test failed in fips mode! ]---
[    1.425439] ------------[ cut here ]------------
[    1.425751] sched: Unexpected reschedule of offline CPU#0!
[    1.426092] WARNING: CPU: 1 PID: 407 at arch/x86/kernel/smp.c:128 native_smp_send_reschedule+0x39/0x40
[    1.426728] Modules linked in: tcrypt(+) authenc cmac wp512 twofish_generic twofish_x86_64_3way twofish_x86_64 twofish_common tea sha512_ssse3 sha512_generic sha256_ssse3 sha1_ssse3 serpent_sse2_x86_64 serpent_generic seed salsa20_generic rmd320 rmd256 rmd160 rmd128 michael_mic md4 khazad fcrypt dm_crypt des3_ede_x86_64 des_generic crc32c_intel ccm cast6_generic cast_common camellia_generic camellia_x86_64 blowfish_generic blowfish_x86_64 blowfish_common arc4 ansi_cprng vfio_pci irqbypass vfio_virqfd vfio_iommu_type1 vfio ipmi_devintf ipmi_msghandler
[    1.428579] CPU: 1 PID: 407 Comm: modprobe Not tainted 4.19.84-300.el7.x86_64 #1
[    1.429074] Hardware name: Nutanix AHV, BIOS 1.11.0-2.el7 04/01/2014
[    1.429404] RIP: 0010:native_smp_send_reschedule+0x39/0x40
[    1.429661] Code: 0f 92 c0 84 c0 74 15 48 8b 05 03 55 14 01 be fd 00 00 00 48 8b 40 30 e9 d5 d7 ba 00 89 fe 48 c7 c7 98 bc 0b af e8 c7 fb 05 00 <0f> 0b c3 0f 1f 40 00 0f 1f 44 00 00 53 48 83 ec 20 65 48 8b 04 25
[    1.430412] RSP: 0018:ffff9a6438043cf8 EFLAGS: 00010082
[    1.430723] RAX: 0000000000000000 RBX: ffff9a642f583b80 RCX: 0000000000000006
[    1.431079] RDX: 0000000000000000 RSI: 0000000000000086 RDI: ffff9a64380568f0
[    1.431464] RBP: 0000000000000000 R08: 0000000000000000 R09: 0000000000000390
[    1.431808] R10: 0000000000000000 R11: 0000000000000000 R12: ffff9a642f58435c
[    1.432167] R13: 0000000000000000 R14: 0000000000000046 R15: 0000000000020400
[    1.432505] FS:  00007faeb367e740(0000) GS:ffff9a6438040000(0000) knlGS:0000000000000000
[    1.433027] CS:  0010 DS: 0000 ES: 0000 CR0: 0000000080050033
[    1.433367] CR2: 00007f73d061c288 CR3: 00000001304d6000 CR4: 00000000003006e0
[    1.433718] Call Trace:
[    1.434014]  <IRQ>
[    1.434310]  try_to_wake_up+0x3f0/0x450
[    1.434623]  __wake_up_common+0x8f/0x160
[    1.434939]  ep_poll_callback+0x1af/0x300
[    1.435263]  __wake_up_common+0x8f/0x160
[    1.435563]  __wake_up_common_lock+0x7a/0xc0
[    1.435895]  irq_work_run_list+0x4c/0x70
[    1.436228]  ? tick_sched_do_timer+0x80/0x80
[    1.436548]  update_process_times+0x3b/0x50
[    1.436854]  tick_sched_handle+0x25/0x60
[    1.437176]  tick_sched_timer+0x37/0x70
[    1.437485]  __hrtimer_run_queues+0xfb/0x270
[    1.437800]  hrtimer_interrupt+0x122/0x270
[    1.438104]  smp_apic_timer_interrupt+0x6a/0x140
[    1.438446]  apic_timer_interrupt+0xf/0x20
[    1.438764]  </IRQ>
[    1.439049] RIP: 0010:panic+0x209/0x25c
[    1.439381] Code: 83 3d 36 d6 8e 01 00 74 05 e8 3f 60 02 00 48 c7 c6 20 32 9a af 48 c7 c7 d0 6e 0c af 31 c0 e8 8e 5e 06 00 fb 66 0f 1f 44 00 00 <45> 31 e4 e8 0b c0 0c 00 4d 39 ec 7c 1e 41 83 f6 01 48 8b 05 db d5
[    1.440264] RSP: 0018:ffffadfd0144baf8 EFLAGS: 00000246 ORIG_RAX: ffffffffffffff13
[    1.440783] RAX: 0000000000000060 RBX: 0000000000000000 RCX: 0000000000000006
[    1.441211] RDX: 0000000000000000 RSI: 0000000000000092 RDI: ffff9a64380568f0
[    1.441639] RBP: ffffadfd0144bb68 R08: 0000000000000000 R09: 000000000000038e
[    1.442034] R10: 0000000000000000 R11: 0000000000000000 R12: ffffffffaf105b38
[    1.442449] R13: 0000000000000000 R14: 0000000000000000 R15: ffffadfd0144be88
[    1.442829]  ? panic+0x202/0x25c
[    1.443135]  alg_test.part.14+0x360/0x380
[    1.443494]  do_test+0x4e74/0x5eed [tcrypt]
[    1.443838]  do_test+0x5edd/0x5eed [tcrypt]
[    1.444160]  ? 0xffffffffc026a000
[    1.444484]  tcrypt_mod_init+0x50/0x1000 [tcrypt]
[    1.444811]  ? 0xffffffffc026a000
[    1.445121]  do_one_initcall+0x4e/0x1d4
[    1.445466]  ? free_unref_page_commit+0x85/0xf0
[    1.445802]  ? _cond_resched+0x15/0x30
[    1.446104]  ? kmem_cache_alloc_trace+0x17f/0x1e0
[    1.446467]  do_init_module+0x5a/0x210
[    1.446789]  load_module.isra.71+0x20cb/0x27e0
[    1.447140]  ? m_show+0x1c0/0x1c0
[    1.447474]  ? vmap_page_range_noflush+0x282/0x420
[    1.447815]  __do_sys_init_module+0x11c/0x180
[    1.448188]  do_syscall_64+0x5b/0x1a0
[    1.448540]  entry_SYSCALL_64_after_hwframe+0x44/0xa9
[    1.448867] RIP: 0033:0x7faeb2b42fca
[    1.449181] Code: 48 8b 0d a9 7e 2c 00 f7 d8 64 89 01 48 83 c8 ff c3 66 2e 0f 1f 84 00 00 00 00 00 0f 1f 44 00 00 49 89 ca b8 af 00 00 00 0f 05 <48> 3d 01 f0 ff ff 73 01 c3 48 8b 0d 76 7e 2c 00 f7 d8 64 89 01 48
[    1.450132] RSP: 002b:00007fff584491d8 EFLAGS: 00000206 ORIG_RAX: 00000000000000af
[    1.450718] RAX: ffffffffffffffda RBX: 0000000000a903d0 RCX: 00007faeb2b42fca
[    1.451076] RDX: 000000000041a96e RSI: 000000000001f05b RDI: 0000000000a9f350
[    1.451493] RBP: 000000000041a96e R08: 0000000000000000 R09: 0000000000a93ee0
[    1.451867] R10: 0000000000a93e00 R11: 0000000000000206 R12: 0000000000a9f350
[    1.452213] R13: 0000000000a905a0 R14: 0000000000040000 R15: 0000000000000000
[    1.452568] ---[ end trace bce24ab776ddad45 ]---
```
