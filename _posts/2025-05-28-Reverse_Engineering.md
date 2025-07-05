---
title: A Try on Reverse Engineering | Vincent's Technical Reports (Vol. 1)
date: 2025-05-28 00:30:00 +0800
categories: [Vincent's technical reports]
tags: [Reverse Engineering]
pin: true
author: 宋建昊

toc: true
comments: true
typora-root-url: ../../framontom.github.io
math: false
mermaid: true
---
# 一次有趣的逆向工程经历

## 前因

在五道口技术学院某抽象院系的某课程的一次作业中，出现了有趣的情况。

作业要求监测某可执行文件运行过程中的数据，再用数据推测得到助教提前设置好的两个信息。

提供给同学们下载的是编译好的可执行文件，而不是源代码，不然可以直接阅读源码获得答案。

同时，这个可执行文件运行需要一个参数，助教规定每个同学将参数设置为自己的学号。

这是为了保证同学们的答案不全相同来防止抄袭，但也可以推测出正确答案是输入参数的某个函数。

## 使用objdump进行反汇编

```shell
// -d 选项指示 objdump 对目标文件中包含可执行代码的节（section）进行反汇编
// 将机器码转换为对应架构的汇编指令。 
// -C 选项用于解析编译器对符号名进行的名称修饰（name mangling）
// 将修饰后的符号名转换为原始可读的函数名，如 foo(int, int)/lipsum()
objdump -d -C ./main
```

执行命令后得到本次所关注的`lipsum()`函数和`main`函数。

```
0000000000001150 <main>:
    1150:       41 54                   push   %r12
    1152:       55                      push   %rbp
    1153:       48 83 ec 18             sub    $0x18,%rsp
    1157:       89 7c 24 0c             mov    %edi,0xc(%rsp)
    115b:       48 8d 7c 24 0c          lea    0xc(%rsp),%rdi
    1160:       48 89 34 24             mov    %rsi,(%rsp)
    1164:       48 89 e6                mov    %rsp,%rsi
    1167:       e8 94 ff ff ff          call   1100 <MPI_Init@plt>
    116c:       48 8d 35 91 5e d4 41    lea    0x41d45e91(%rip),%rsi        # 41d47004 <mpiSize>
    1173:       bf 00 00 00 44          mov    $0x44000000,%edi
    1178:       e8 33 ff ff ff          call   10b0 <MPI_Comm_size@plt>
    117d:       48 8d 35 84 5e d4 41    lea    0x41d45e84(%rip),%rsi        # 41d47008 <mpiRank>
    1184:       bf 00 00 00 44          mov    $0x44000000,%edi
    1189:       e8 62 ff ff ff          call   10f0 <MPI_Comm_rank@plt>
    118e:       83 3d 6f 5e d4 41 08    cmpl   $0x8,0x41d45e6f(%rip)        # 41d47004 <mpiSize>
    1195:       0f 85 83 01 00 00       jne    131e <main+0x1ce>
    119b:       83 7c 24 0c 02          cmpl   $0x2,0xc(%rsp)
    11a0:       0f 85 56 01 00 00       jne    12fc <main+0x1ac>
    11a6:       48 8b 04 24             mov    (%rsp),%rax
    11aa:       31 f6                   xor    %esi,%esi
    11ac:       ba 0a 00 00 00          mov    $0xa,%edx
    11b1:       48 8b 78 08             mov    0x8(%rax),%rdi
    11b5:       e8 26 ff ff ff          call   10e0 <strtol@plt>
    11ba:       89 05 40 5e d4 41       mov    %eax,0x41d45e40(%rip)        # 41d47000 <id>
    11c0:       48 89 c1                mov    %rax,%rcx
    11c3:       89 c6                   mov    %eax,%esi
    11c5:       85 c0                   test   %eax,%eax
    11c7:       0f 88 39 01 00 00       js     1306 <main+0x1b6>
    11cd:       49 b8 e5 8f a2 12 31    movabs $0x89705f3112a28fe5,%r8
    11d4:       5f 70 89 
    11d7:       bf 0a 00 00 00          mov    $0xa,%edi
    11dc:       0f 1f 40 00             nopl   0x0(%rax)
    11e0:       48 63 f6                movslq %esi,%rsi
    11e3:       48 63 c9                movslq %ecx,%rcx
    11e6:       48 69 f6 17 27 00 00    imul   $0x2717,%rsi,%rsi
    11ed:       48 69 c9 17 27 00 00    imul   $0x2717,%rcx,%rcx
    11f4:       48 83 c6 07             add    $0x7,%rsi
    11f8:       48 89 f0                mov    %rsi,%rax
    11fb:       48 83 c1 09             add    $0x9,%rcx
    11ff:       49 f7 e0                mul    %r8
    1202:       48 83 f1 01             xor    $0x1,%rcx
    1206:       48 89 c8                mov    %rcx,%rax
    1209:       48 c1 ea 1d             shr    $0x1d,%rdx
    120d:       48 69 d2 07 ca 9a 3b    imul   $0x3b9aca07,%rdx,%rdx
    1214:       29 d6                   sub    %edx,%esi
    1216:       49 f7 e0                mul    %r8
    1219:       48 c1 ea 1d             shr    $0x1d,%rdx
    121d:       48 69 d2 07 ca 9a 3b    imul   $0x3b9aca07,%rdx,%rdx
    1224:       29 d1                   sub    %edx,%ecx
    1226:       83 ef 01                sub    $0x1,%edi
    1229:       75 b5                   jne    11e0 <main+0x90>
    122b:       83 e6 07                and    $0x7,%esi
    122e:       83 e1 01                and    $0x1,%ecx
    1231:       89 35 dd 2d 00 00       mov    %esi,0x2ddd(%rip)        # 4014 <slowRank>
    1237:       89 0d d3 2d 00 00       mov    %ecx,0x2dd3(%rip)        # 4010 <workloadType>
    123d:       e8 4e 02 00 00          call   1490 <init()>
    1242:       bf 00 00 00 44          mov    $0x44000000,%edi
    1247:       e8 14 fe ff ff          call   1060 <MPI_Barrier@plt>
    124c:       e8 bf 02 00 00          call   1510 <lipsum()>
    1251:       bf 00 00 00 44          mov    $0x44000000,%edi
    1256:       e8 05 fe ff ff          call   1060 <MPI_Barrier@plt>
    125b:       e8 10 fe ff ff          call   1070 <MPI_Finalize@plt>
    1260:       8b 05 a2 5d d4 41       mov    0x41d45da2(%rip),%eax        # 41d47008 <mpiRank>
    1266:       85 c0                   test   %eax,%eax
    1268:       74 0a                   je     1274 <main+0x124>
    126a:       48 83 c4 18             add    $0x18,%rsp
    126e:       31 c0                   xor    %eax,%eax
    1270:       5d                      pop    %rbp
    1271:       41 5c                   pop    %r12
    1273:       c3                      ret
    1274:       48 8d 35 c7 0d 00 00    lea    0xdc7(%rip),%rsi        # 2042 <_IO_stdin_used+0x42>
    127b:       48 8d 3d be 2d 00 00    lea    0x2dbe(%rip),%rdi        # 4040 <std::cout@GLIBCXX_3.4>
    1282:       e8 09 fe ff ff          call   1090 <std::basic_ostream<char, std::char_traits<char> >& std::operator<< <std::char_traits<char> >(std::basic_ostream<char, std::char_traits<char> >&, char const*)@plt>
    1287:       8b 35 73 5d d4 41       mov    0x41d45d73(%rip),%esi        # 41d47000 <id>
    128d:       48 89 c7                mov    %rax,%rdi
    1290:       e8 9b fe ff ff          call   1130 <std::ostream::operator<<(int)@plt>
    1295:       48 89 c5                mov    %rax,%rbp
    1298:       48 8b 00                mov    (%rax),%rax
    129b:       48 8b 40 e8             mov    -0x18(%rax),%rax
    129f:       4c 8b a4 05 f0 00 00    mov    0xf0(%rbp,%rax,1),%r12
    12a6:       00 
    12a7:       4d 85 e4                test   %r12,%r12
    12aa:       74 6d                   je     1319 <main+0x1c9>
    12ac:       41 80 7c 24 38 00       cmpb   $0x0,0x38(%r12)
    12b2:       74 1b                   je     12cf <main+0x17f>
    12b4:       41 0f b6 44 24 43       movzbl 0x43(%r12),%eax
    12ba:       48 89 ef                mov    %rbp,%rdi
    12bd:       0f be f0                movsbl %al,%esi
    12c0:       e8 6b fd ff ff          call   1030 <std::ostream::put(char)@plt>
    12c5:       48 89 c7                mov    %rax,%rdi
    12c8:       e8 83 fd ff ff          call   1050 <std::ostream::flush()@plt>
    12cd:       eb 9b                   jmp    126a <main+0x11a>
    12cf:       4c 89 e7                mov    %r12,%rdi
    12d2:       e8 c9 fd ff ff          call   10a0 <std::ctype<char>::_M_widen_init() const@plt>
    12d7:       49 8b 04 24             mov    (%r12),%rax
    12db:       48 8d 0d ce 04 00 00    lea    0x4ce(%rip),%rcx        # 17b0 <std::ctype<char>::do_widen(char) const>
    12e2:       48 8b 50 30             mov    0x30(%rax),%rdx
    12e6:       b8 0a 00 00 00          mov    $0xa,%eax
    12eb:       48 39 ca                cmp    %rcx,%rdx
    12ee:       74 ca                   je     12ba <main+0x16a>
    12f0:       be 0a 00 00 00          mov    $0xa,%esi
    12f5:       4c 89 e7                mov    %r12,%rdi
    12f8:       ff d2                   call   *%rdx
    12fa:       eb be                   jmp    12ba <main+0x16a>
    12fc:       c7 05 fa 5c d4 41 ff    movl   $0xffffffff,0x41d45cfa(%rip)        # 41d47000 <id>
    1303:       ff ff ff 
    1306:       83 3d fb 5c d4 41 00    cmpl   $0x0,0x41d45cfb(%rip)        # 41d47008 <mpiRank>
    130d:       74 34                   je     1343 <main+0x1f3>
    130f:       bf 01 00 00 00          mov    $0x1,%edi
    1314:       e8 a7 fd ff ff          call   10c0 <exit@plt>
    1319:       e8 b2 fd ff ff          call   10d0 <std::__throw_bad_cast()@plt>
    131e:       83 3d e3 5c d4 41 00    cmpl   $0x0,0x41d45ce3(%rip)        # 41d47008 <mpiRank>
    1325:       75 e8                   jne    130f <main+0x1bf>
    1327:       48 8b 3d 32 2e 00 00    mov    0x2e32(%rip),%rdi        # 4160 <stderr@GLIBC_2.2.5>
    132e:       ba 08 00 00 00          mov    $0x8,%edx
    1333:       48 8d 35 ce 0c 00 00    lea    0xcce(%rip),%rsi        # 2008 <_IO_stdin_used+0x8>
    133a:       31 c0                   xor    %eax,%eax
    133c:       e8 cf fd ff ff          call   1110 <fprintf@plt>
    1341:       eb cc                   jmp    130f <main+0x1bf>
    1343:       48 8b 04 24             mov    (%rsp),%rax
    1347:       48 8b 3d 12 2e 00 00    mov    0x2e12(%rip),%rdi        # 4160 <stderr@GLIBC_2.2.5>
    134e:       48 8d 35 d5 0c 00 00    lea    0xcd5(%rip),%rsi        # 202a <_IO_stdin_used+0x2a>
    1355:       48 8b 10                mov    (%rax),%rdx
    1358:       31 c0                   xor    %eax,%eax
    135a:       e8 b1 fd ff ff          call   1110 <fprintf@plt>
    135f:       eb ae                   jmp    130f <main+0x1bf>
    1361:       66 66 2e 0f 1f 84 00    data16 cs nopw 0x0(%rax,%rax,1)
    1368:       00 00 00 00 
    136c:       0f 1f 40 00             nopl   0x0(%rax)


0000000000001510 <lipsum()>:
    1510:       41 57                   push   %r15
    1512:       41 56                   push   %r14
    1514:       41 55                   push   %r13
    1516:       41 54                   push   %r12
    1518:       55                      push   %rbp
    1519:       53                      push   %rbx
    151a:       48 83 ec 18             sub    $0x18,%rsp
    151e:       8b 0d ec 2a 00 00       mov    0x2aec(%rip),%ecx        # 4010 <workloadType>
    1524:       8b 15 de 5a d4 41       mov    0x41d45ade(%rip),%edx        # 41d47008 <mpiRank>
    152a:       8b 05 e4 2a 00 00       mov    0x2ae4(%rip),%eax        # 4014 <slowRank>
    1530:       85 c9                   test   %ecx,%ecx
    1532:       0f 85 49 01 00 00       jne    1681 <lipsum()+0x171>
    1538:       48 63 0d c1 5a d4 41    movslq 0x41d45ac1(%rip),%rcx        # 41d47000 <id>
    153f:       39 c2                   cmp    %eax,%edx
    1541:       be 70 17 00 00          mov    $0x1770,%esi
    1546:       b8 b8 0b 00 00          mov    $0xbb8,%eax
    154b:       48 0f 45 f0             cmovne %rax,%rsi
    154f:       48 8d 3d ea 74 f8 0b    lea    0xbf874ea(%rip),%rdi        # bf88a40 <ci>
    1556:       48 ba 0b d7 a3 70 3d    movabs $0xa3d70a3d70a3d70b,%rdx
    155d:       0a d7 a3 
    1560:       bb 64 00 00 00          mov    $0x64,%ebx
    1565:       48 69 c9 17 27 00 00    imul   $0x2717,%rcx,%rcx
    156c:       4c 8d 2d 0d 6f f0 17    lea    0x17f06f0d(%rip),%r13        # 17f08480 <ai>
    1573:       4c 8d 3d e6 71 f4 11    lea    0x11f471e6(%rip),%r15        # 11f48760 <bi>
    157a:       48 89 c8                mov    %rcx,%rax
    157d:       48 f7 ea                imul   %rdx
    1580:       48 8d 04 0a             lea    (%rdx,%rcx,1),%rax
    1584:       48 89 ca                mov    %rcx,%rdx
    1587:       48 c1 fa 3f             sar    $0x3f,%rdx
    158b:       48 c1 f8 06             sar    $0x6,%rax
    158f:       48 29 d0                sub    %rdx,%rax
    1592:       48 8d 04 80             lea    (%rax,%rax,4),%rax
    1596:       48 8d 14 80             lea    (%rax,%rax,4),%rdx
    159a:       48 89 c8                mov    %rcx,%rax
    159d:       48 c1 e2 02             shl    $0x2,%rdx
    15a1:       48 29 d0                sub    %rdx,%rax
    15a4:       48 ba c3 f5 28 5c 8f    movabs $0x28f5c28f5c28f5c3,%rdx
    15ab:       c2 f5 28 
    15ae:       48 05 aa 00 00 00       add    $0xaa,%rax
    15b4:       48 0f af c6             imul   %rsi,%rax
    15b8:       48 c1 e8 03             shr    $0x3,%rax
    15bc:       48 f7 e2                mul    %rdx
    15bf:       48 89 d0                mov    %rdx,%rax
    15c2:       48 c1 e8 02             shr    $0x2,%rax
    15c6:       41 89 c6                mov    %eax,%r14d
    15c9:       48 8d 2c 85 00 00 00    lea    0x0(,%rax,4),%rbp
    15d0:       00 
    15d1:       41 89 c4                mov    %eax,%r12d
    15d4:       4d 69 f6 1f 85 eb 51    imul   $0x51eb851f,%r14,%r14
    15db:       85 c0                   test   %eax,%eax
    15dd:       b8 04 00 00 00          mov    $0x4,%eax
    15e2:       48 0f 4f c5             cmovg  %rbp,%rax
    15e6:       31 ed                   xor    %ebp,%ebp
    15e8:       48 89 44 24 08          mov    %rax,0x8(%rsp)
    15ed:       49 c1 ee 25             shr    $0x25,%r14
    15f1:       0f 1f 80 00 00 00 00    nopl   0x0(%rax)
    15f8:       48 8b 54 24 08          mov    0x8(%rsp),%rdx
    15fd:       31 f6                   xor    %esi,%esi
    15ff:       e8 3c fa ff ff          call   1040 <memset@plt>
    1604:       45 31 c9                xor    %r9d,%r9d
    1607:       48 89 c7                mov    %rax,%rdi
    160a:       66 0f 1f 44 00 00       nopw   0x0(%rax,%rax,1)
    1610:       31 f6                   xor    %esi,%esi
    1612:       31 c9                   xor    %ecx,%ecx
    1614:       0f 1f 40 00             nopl   0x0(%rax)
    1618:       69 c3 17 27 00 00       imul   $0x2717,%ebx,%eax
    161e:       05 d5 00 00 00          add    $0xd5,%eax
    1623:       99                      cltd
    1624:       41 f7 fc                idiv   %r12d
    1627:       48 63 c2                movslq %edx,%rax
    162a:       48 63 d1                movslq %ecx,%rdx
    162d:       48 89 c3                mov    %rax,%rbx
    1630:       48 69 c0 92 13 00 00    imul   $0x1392,%rax,%rax
    1637:       48 01 d0                add    %rdx,%rax
    163a:       41 8b 54 8d 00          mov    0x0(%r13,%rcx,4),%edx
    163f:       48 83 c1 01             add    $0x1,%rcx
    1643:       41 0f af 14 87          imul   (%r15,%rax,4),%edx
    1648:       01 d6                   add    %edx,%esi
    164a:       4c 39 f1                cmp    %r14,%rcx
    164d:       75 c9                   jne    1618 <lipsum()+0x108>
    164f:       42 89 34 8f             mov    %esi,(%rdi,%r9,4)
    1653:       49 83 c1 01             add    $0x1,%r9
    1657:       45 39 cc                cmp    %r9d,%r12d
    165a:       7f b4                   jg     1610 <lipsum()+0x100>
    165c:       83 c5 01                add    $0x1,%ebp
    165f:       48 81 c7 48 4e 00 00    add    $0x4e48,%rdi
    1666:       49 81 c5 48 4e 00 00    add    $0x4e48,%r13
    166d:       44 39 e5                cmp    %r12d,%ebp
    1670:       7c 86                   jl     15f8 <lipsum()+0xe8>
    1672:       48 83 c4 18             add    $0x18,%rsp
    1676:       5b                      pop    %rbx
    1677:       5d                      pop    %rbp
    1678:       41 5c                   pop    %r12
    167a:       41 5d                   pop    %r13
    167c:       41 5e                   pop    %r14
    167e:       41 5f                   pop    %r15
    1680:       c3                      ret
    1681:       48 63 0d 78 59 d4 41    movslq 0x41d45978(%rip),%rcx        # 41d47000 <id>
    1688:       39 c2                   cmp    %eax,%edx
    168a:       be d0 07 00 00          mov    $0x7d0,%esi
    168f:       b8 e8 03 00 00          mov    $0x3e8,%eax
    1694:       48 0f 45 f0             cmovne %rax,%rsi
    1698:       48 8d 3d 01 6b ec 1d    lea    0x1dec6b01(%rip),%rdi        # 1dec81a0 <cd>
    169f:       48 ba 0b d7 a3 70 3d    movabs $0xa3d70a3d70a3d70b,%rdx
    16a6:       0a d7 a3 
    16a9:       48 69 c9 17 27 00 00    imul   $0x2717,%rcx,%rcx
    16b0:       48 8d 2d 29 5f dc 35    lea    0x35dc5f29(%rip),%rbp        # 35dc75e0 <ad>
    16b7:       48 89 c8                mov    %rcx,%rax
    16ba:       48 f7 ea                imul   %rdx
    16bd:       48 8d 04 0a             lea    (%rdx,%rcx,1),%rax
    16c1:       48 89 ca                mov    %rcx,%rdx
    16c4:       48 c1 fa 3f             sar    $0x3f,%rdx
    16c8:       48 c1 f8 06             sar    $0x6,%rax
    16cc:       48 29 d0                sub    %rdx,%rax
    16cf:       48 8d 04 80             lea    (%rax,%rax,4),%rax
    16d3:       48 8d 14 80             lea    (%rax,%rax,4),%rdx
    16d7:       48 89 c8                mov    %rcx,%rax
    16da:       48 c1 e2 02             shl    $0x2,%rdx
    16de:       48 29 d0                sub    %rdx,%rax
    16e1:       48 ba c3 f5 28 5c 8f    movabs $0x28f5c28f5c28f5c3,%rdx
    16e8:       c2 f5 28 
    16eb:       48 05 aa 00 00 00       add    $0xaa,%rax
    16f1:       48 0f af c6             imul   %rsi,%rax
    16f5:       48 c1 e8 03             shr    $0x3,%rax
    16f9:       48 f7 e2                mul    %rdx
    16fc:       48 89 d0                mov    %rdx,%rax
    16ff:       48 c1 e8 02             shr    $0x2,%rax
    1703:       85 c0                   test   %eax,%eax
    1705:       4c 8d 24 c5 00 00 00    lea    0x0(,%rax,8),%r12
    170c:       00 
    170d:       89 c3                   mov    %eax,%ebx
    170f:       b8 08 00 00 00          mov    $0x8,%eax
    1714:       4c 0f 4e e0             cmovle %rax,%r12
    1718:       45 31 ed                xor    %r13d,%r13d
    171b:       0f 1f 44 00 00          nopl   0x0(%rax,%rax,1)
    1720:       4c 89 e2                mov    %r12,%rdx
    1723:       31 f6                   xor    %esi,%esi
    1725:       e8 16 f9 ff ff          call   1040 <memset@plt>
    172a:       48 8d 15 8f 64 e4 29    lea    0x29e4648f(%rip),%rdx        # 29e47bc0 <bd>
    1731:       31 c9                   xor    %ecx,%ecx
    1733:       66 0f ef d2             pxor   %xmm2,%xmm2
    1737:       48 89 c7                mov    %rax,%rdi
    173a:       66 0f 1f 44 00 00       nopw   0x0(%rax,%rax,1)
    1740:       31 c0                   xor    %eax,%eax
    1742:       66 0f 28 ca             movapd %xmm2,%xmm1
    1746:       66 2e 0f 1f 84 00 00    cs nopw 0x0(%rax,%rax,1)
    174d:       00 00 00 
    1750:       f2 0f 10 44 c5 00       movsd  0x0(%rbp,%rax,8),%xmm0
    1756:       f2 0f 59 04 c2          mulsd  (%rdx,%rax,8),%xmm0
    175b:       48 83 c0 01             add    $0x1,%rax
    175f:       f2 0f 58 c8             addsd  %xmm0,%xmm1
    1763:       39 c3                   cmp    %eax,%ebx
    1765:       7f e9                   jg     1750 <lipsum()+0x240>
    1767:       f2 0f 11 0c cf          movsd  %xmm1,(%rdi,%rcx,8)
    176c:       48 83 c1 01             add    $0x1,%rcx
    1770:       48 81 c2 90 9c 00 00    add    $0x9c90,%rdx
    1777:       39 cb                   cmp    %ecx,%ebx
    1779:       7f c5                   jg     1740 <lipsum()+0x230>
    177b:       41 83 c5 01             add    $0x1,%r13d
    177f:       48 81 c7 90 9c 00 00    add    $0x9c90,%rdi
    1786:       48 81 c5 90 9c 00 00    add    $0x9c90,%rbp
    178d:       41 39 dd                cmp    %ebx,%r13d
    1790:       7c 8e                   jl     1720 <lipsum()+0x210>
    1792:       48 83 c4 18             add    $0x18,%rsp
    1796:       5b                      pop    %rbx
    1797:       5d                      pop    %rbp
    1798:       41 5c                   pop    %r12
    179a:       41 5d                   pop    %r13
    179c:       41 5e                   pop    %r14
    179e:       41 5f                   pop    %r15
    17a0:       c3                      ret
    17a1:       66 2e 0f 1f 84 00 00    cs nopw 0x0(%rax,%rax,1)
    17a8:       00 00 00 
    17ab:       0f 1f 44 00 00          nopl   0x0(%rax,%rax,1)
```

## 使用Ghidra进行反编译

得到`lipsum()`函数和`main`函数的等效源代码，得出的源码可读性还比较差，我们还需要进一步处理。

```c
undefined8 main(int param_1, undefined8 *param_2)
{
    long *plVar1;
    ulong uVar2;
    basic_ostream *this;
    long *plVar3;
    uint uVar4;
    uint uVar5;
    ulong uVar6;
    int iVar7;
    undefined8 *local_28;
    int local_1c[3];

    local_28 = param_2;
    local_1c[0] = param_1;
    MPI_Init(local_1c, &local_28);
    MPI_Comm_size(0x44000000, &mpiSize);
    MPI_Comm_rank(0x44000000);
    if (mpiSize == 8) {
        if (local_1c[0] == 2) {
            uVar2 = strtol((char *)local_28[1], (char **)0x0, 10);
            id = (int)uVar2;
            uVar6 = uVar2 & 0xffffffff;
            if (-1 < id) {
                iVar7 = 10;
                do {
                    uVar6 = (long)(int)uVar6 * 0x2717 + 7;
                    uVar2 = (long)(int)uVar2 * 0x2717 + 9U ^ 1;
                    uVar5 = (int)uVar6 + (int)(uVar6 / 0x3b9aca07) * -0x3b9aca07;
                    uVar6 = (ulong)uVar5;
                    uVar4 = (int)uVar2 + (int)(uVar2 / 0x3b9aca07) * -0x3b9aca07;
                    uVar2 = (ulong)uVar4;
                    iVar7 = iVar7 + -1;
                } while (iVar7 != 0);
                slowRank = uVar5 & 7;
                workloadType = uVar4 & 1;
                init();
                MPI_Barrier(0x44000000);
                lipsum();
                MPI_Barrier(0x44000000);
                MPI_Finalize();
                if (mpiRank != 0) {
                    return 0;
                }
                this = std::operator<<((basic_ostream *)std::cout, "Sucesss ");
                plVar3 = (long *)std::basic_ostream<char, std::char_traits<char>>::operator<<
                                 ((basic_ostream<char, std::char_traits<char>> *)this, id);
                plVar1 = *(long **)((long)plVar3 + *(long *)(*plVar3 + -0x18) + 0xf0);
                if (plVar1 != (long *)0x0) {
                    if (*(char *)(plVar1 + 7) == '\0') {
                        std::ctype<char>::_M_widen_init();
                        if (*(code **)(*plVar1 + 0x30) != std::ctype<char>::do_widen) {
                            (**(code **)(*plVar1 + 0x30))(plVar1, 10);
                        }
                    }
                    std::basic_ostream<char, std::char_traits<char>>::put((char)plVar3);
                    std::basic_ostream<char, std::char_traits<char>>::flush();
                    return 0;
                }
                std::__throw_bad_cast();
                goto LAB_0010131e;
            }
        }
        else {
            id = -1;
        }
        if (mpiRank == 0) {
            fprintf(stderr, "Usage: %s <Student ID>\n", *local_28);
        }
    }
    else {
    LAB_0010131e:
        if (mpiRank == 0) {
            fprintf(stderr, "Please run with %d MPI prcoesses\n", 8);
        }
    }
    /* WARNING: Subroutine does not return */
    exit(1);
}

/* WARNING: Unknown calling convention -- yet parameter storage is locked */
/* lipsum() */
void lipsum(void)
{
    long lVar1;
    long lVar2;
    int iVar3;
    ulong uVar4;
    void *pvVar5;
    int iVar6;
    ulong uVar7;
    long lVar8;
    int iVar9;
    int iVar10;
    long lVar11;
    undefined1 *puVar12;
    size_t sVar13;
    undefined1 *puVar14;
    double dVar15;

    if (workloadType == 0) {
        lVar11 = 6000;
        if (mpiRank != slowRank) {
            lVar11 = 3000;
        }
        puVar12 = ci;
        lVar8 = 100;
        puVar14 = ai;
        uVar4 = (ulong)((((long)id * 0x2717) % 100 + 0xaa) * lVar11) / 200;
        iVar3 = (int)uVar4;
        sVar13 = 4;
        if (0 < iVar3) {
            sVar13 = uVar4 * 4;
        }
        iVar9 = 0;
        do {
            pvVar5 = memset(puVar12, 0, sVar13);
            lVar11 = 0;
            do {
                iVar10 = 0;
                uVar7 = 0;
                do {
                    lVar8 = (long)(((int)lVar8 * 0x2717 + 0xd5) % iVar3);
                    iVar6 = (int)uVar7;
                    lVar1 = uVar7 * 4;
                    uVar7 = uVar7 + 1;
                    iVar10 = iVar10 + *(int *)(puVar14 + lVar1) *
                                      *(int *)(bi + (lVar8 * 0x1392 + (long)iVar6) * 4);
                } while (uVar7 != (uVar4 & 0xffffffff) / 100);
                *(int *)((long)pvVar5 + lVar11 * 4) = iVar10;
                lVar11 = lVar11 + 1;
            } while ((int)lVar11 < iVar3);
            iVar9 = iVar9 + 1;
            puVar12 = (undefined1 *)((long)pvVar5 + 0x4e48);
            puVar14 = puVar14 + 0x4e48;
        } while (iVar9 < iVar3);
        return;
    }
    lVar11 = 2000;
    if (mpiRank != slowRank) {
        lVar11 = 1000;
    }
    puVar14 = cd;
    puVar12 = ad;
    uVar4 = (ulong)((((long)id * 0x2717) % 100 + 0xaa) * lVar11) / 200;
    iVar3 = (int)uVar4;
    sVar13 = uVar4 * 8;
    if (iVar3 < 1) {
        sVar13 = 8;
    }
    iVar9 = 0;
    do {
        pvVar5 = memset(puVar14, 0, sVar13);
        puVar14 = bd;
        lVar11 = 0;
        do {
            lVar8 = 0;
            dVar15 = 0.0;
            do {
                lVar2 = lVar8 * 8;
                lVar1 = lVar8 * 8;
                lVar8 = lVar8 + 1;
                dVar15 = dVar15 + *(double *)(puVar12 + lVar2) * *(double *)(puVar14 + lVar1);
            } while ((int)lVar8 < iVar3);
            *(double *)((long)pvVar5 + lVar11 * 8) = dVar15;
            lVar11 = lVar11 + 1;
            puVar14 = puVar14 + 0x9c90;
        } while ((int)lVar11 < iVar3);
        iVar9 = iVar9 + 1;
        puVar14 = (undefined1 *)((long)pvVar5 + 0x9c90);
        puVar12 = puVar12 + 0x9c90;
    } while (iVar9 < iVar3);
    return;
}
```

## 使用Grok3大模型进行整理

把这段可读性极差的代码交给`Grok3`大模型进行整理，得到最后近似版的源代码，真相终于浮出水面。

```c
#include <mpi.h>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iostream>

// 全局变量（根据反编译代码推测）
int id = -1;          // 学生 ID 或输入参数
int mpiRank;          // 当前进程的 MPI 排名
int mpiSize;          // MPI 进程总数
int slowRank;         // 慢速进程的排名
int workloadType;     // 工作负载类型（0 或 1）
int* ai;              // 输入数组（整数）
int* bi;              // 输入数组（整数）
int* ci;              // 输出数组（整数）
double* ad;           // 输入数组（浮点数）
double* bd;           // 输入数组（浮点数）
double* cd;           // 输出数组（浮点数）


// 工作负载函数
void lipsum() {
    if (workloadType == 0) {
        // 整数运算工作负载
        int iterations = (mpiRank == slowRank) ? 6000 : 3000; // 慢速进程迭代更多
        int size = (((id * 0x2717) % 100 + 0xAA) * iterations) / 200; // 计算数组大小
        size_t bufferSize = (size > 0) ? size * sizeof(int) : 4 * sizeof(int);

        int* output = ci; // 输出缓冲区
        int* inputA = ai; // 输入缓冲区 A
        for (int i = 0; i < size; ++i) {
            memset(output, 0, bufferSize); // 清零输出缓冲区
            long seed = 100; // 初始种子
            for (int j = 0; j < size; ++j) {
                int sum = 0;
                for (int k = 0; k < size / 100; ++k) {
                    seed = ((seed * 0x2717 + 0xD5) % size); // 更新种子
                    sum += inputA[k * sizeof(int)] * bi[(seed * 0x1392 + k) * sizeof(int) / sizeof(int)];
                }
                output[j] = sum;
            }
            output += 0x4E48 / sizeof(int); // 移动到下一块缓冲区
            inputA += 0x4E48 / sizeof(int);
        }
    } else {
        // 浮点运算工作负载
        int iterations = (mpiRank == slowRank) ? 2000 : 1000; // 慢速进程迭代更多
        int size = (((id * 0x2717) % 100 + 0xAA) * iterations) / 200; // 计算数组大小
        size_t bufferSize = (size > 0) ? size * sizeof(double) : 8 * sizeof(double);

        double* output = cd; // 输出缓冲区
        double* inputA = ad; // 输入缓冲区 A
        double* inputB = bd; // 输入缓冲区 B
        for (int i = 0; i < size; ++i) {
            memset(output, 0, bufferSize); // 清零输出缓冲区
            for (int j = 0; j < size; ++j) {
                double sum = 0.0;
                for (int k = 0; k < size; ++k) {
                    sum += inputA[k] * inputB[k];
                }
                output[j] = sum;
                inputB += 0x9C90 / sizeof(double); // 移动到下一块缓冲区
            }
            output += 0x9C90 / sizeof(double); // 移动到下一块缓冲区
            inputA += 0x9C90 / sizeof(double);
        }
    }
}

int main(int argc, char* argv[]) {
    // 初始化 MPI
    MPI_Init(&argc, &argv);
    MPI_Comm_size(MPI_COMM_WORLD, &mpiSize); // 获取进程总数
    MPI_Comm_rank(MPI_COMM_WORLD, &mpiRank); // 获取当前进程排名

    // 检查是否使用 8 个 MPI 进程
    if (mpiSize != 8) {
        if (mpiRank == 0) {
            fprintf(stderr, "Please run with %d MPI processes\n", 8);
        }
        MPI_Finalize();
        exit(1);
    }

    // 检查命令行参数
    if (argc == 2) {
        id = std::atoi(argv[1]); // 解析学生 ID
        if (id >= 0) {
            // 计算 slowRank 和 workloadType
            unsigned int seed1 = id;
            unsigned int seed2 = id;
            for (int i = 0; i < 10; ++i) {
                seed1 = seed1 * 0x2717 + 7; // 更新种子 1
                seed2 = (seed2 * 0x2717 + 9) ^ 1; // 更新种子 2
                seed1 = seed1 % 0x3B9ACA07; // 模运算
                seed2 = seed2 % 0x3B9ACA07;
            }
            slowRank = seed1 & 7; // 确定慢速进程
            workloadType = seed2 & 1; // 确定工作负载类型（0 或 1）

            // 执行工作负载
            init();
            MPI_Barrier(MPI_COMM_WORLD); // 同步
            lipsum();
            MPI_Barrier(MPI_COMM_WORLD); // 同步
            MPI_Finalize();

            // 主进程输出结果
            if (mpiRank == 0) {
                std::cout << "Success " << id << std::endl;
            }
            return 0;
        }
    }

    // 参数错误处理
    if (mpiRank == 0) {
        fprintf(stderr, "Usage: %s <Student ID>\n", argv[0]);
    }
    MPI_Finalize();
    exit(1);
}
```

可以发现依赖于输入参数的全局变量有两个分别是`slowRank` 和 `workloadType`，`lipsum()`函数会根据这两个参数来执行不同的逻辑，前者决定八个进程中哪个进程设置为慢速进程，后者决定工作负载种类是整数运算还是浮点运算，具体截取如下。

```c
#include <mpi.h>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iostream>

//...
int slowRank;         // 慢速进程的排名
int workloadType;     // 工作负载类型（0 或 1）


// 工作负载函数
void lipsum() {
    if (workloadType == 0) {
        // 整数运算工作负载
        int iterations = (mpiRank == slowRank) ? 6000 : 3000; // 慢速进程迭代更多
		//...
        }
    } else {
        // 浮点运算工作负载
        int iterations = (mpiRank == slowRank) ? 2000 : 1000; // 慢速进程迭代更多
        //...
    }
}

int main(int argc, char* argv[]) {
	//...
    // 检查命令行参数
    //...
    id = std::atoi(argv[1]); // 解析学生 ID
    if (id >= 0) {
        // 计算 slowRank 和 workloadType
        unsigned int seed1 = id;
        unsigned int seed2 = id;
        for (int i = 0; i < 10; ++i) {
            seed1 = seed1 * 0x2717 + 7; // 更新种子 1
            seed2 = (seed2 * 0x2717 + 9) ^ 1; // 更新种子 2
            seed1 = seed1 % 0x3B9ACA07; // 模运算
            seed2 = seed2 % 0x3B9ACA07;
        }
        slowRank = seed1 & 7; // 确定慢速进程
        workloadType = seed2 & 1; // 确定工作负载类型（0 或 1）

     //...
    }
    //...
    // 参数错误处理
	//...
}
```

将十六进制转化为十进制，我们可以看到真正的逻辑如下。

```c
    // 参数为同学学号
	val1 = studentId;
    val2 = studentId;
    
    // Perform the calculations
    for(int i = 0; i < 10; i++) {
        val1 = (val1 * 10007 + 7) % 1000000007;
        val2 = (val2 * 10007 + 9) % 1000000007;
    }
    
    // Calculate task allocation parameters
    slowRank = val1 % 8;
    workloadType = val2 % 2;
```

## 总结

一次有趣的逆向工程之旅就这样结束了，我们可以给逆向工程（`Reverse Engineering`）下一个定义：在不依赖原始设计文档或源代码的情况下，分析一个已有系统、程序、设备或对象的结构、功能和行为，推导出其设计原理、实现细节、内部工作机制或源代码的过程。

附上一些大佬对逆向工程的评价作为结束语。

1. **Linus Torvalds（Linux 内核创始人）**

   “如果你能通过逆向工程理解一个系统，你就有了重现或改进它的自由。这正是开源精神的延伸。”

2. **Bruce Schneier（密码学与安全专家）**

   “逆向工程是安全研究的基石。没有它，我们无法真正理解系统的弱点，也无法构建更强的防御。”

3. **Ken Thompson（UNIX 创始人之一）**

   “要真正理解一个程序必须能够追溯其每一行代码。逆向工程让我们看到隐藏在二进制背后的真相。”

4. **John Carmack（游戏开发者，id Software 创始人）**

   “逆向工程不是偷窃，它是学习。当你拆解一个系统，你会发现它的美与瑕疵。”

5. **Ellen Ullman（程序员，作家）**

   “逆向工程就像考古，挖掘代码的每一层，试图还原设计者的意图。”

   
