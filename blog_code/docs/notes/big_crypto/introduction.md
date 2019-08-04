
搬运自这位大佬[博客](https://hackfun.org/2017/02/22/CTF%E4%B8%AD%E9%82%A3%E4%BA%9B%E8%84%91%E6%B4%9E%E5%A4%A7%E5%BC%80%E7%9A%84%E7%BC%96%E7%A0%81%E5%92%8C%E5%8A%A0%E5%AF%86/)

## CTF中那些脑洞大开的编码和加密

## 0x00 前言

正文开始之前先闲扯几句吧，玩CTF的小伙伴也许会遇到类似这样的问题:表哥，你知道这是什么加密吗？其实CTF中脑洞密码题(非现代加密方式)一般都是各种古典密码的变形，一般出题者会对密文进行一些处理，但是会给留一些线索，所以写此文的目的是想给小伙伴做题时给一些参考，当然常在CTF里出现的编码也可以了解一下。本来是想尽快写出参考的文章，无奈期间被各种事情耽搁导致文章断断续续写了2个月，文章肯定有许多没有提及到，欢迎小伙伴补充，总之，希望对小伙伴们有帮助吧！最后欢迎小伙伴来 [博客](https://www.hackfun.org/) 玩耍:P

## 0x01 目录

1. 常见编码:
   1. ASCII编码
   2. Base64/32/16编码
   3. shellcode编码
   4. Quoted-printable编码
   5. XXencode编码
   6. UUencode编码
   7. URL编码
   8. Unicode编码
   9. Escape/Unescape编码
   10. HTML实体编码
   11. 敲击码(Tap code)
   12. 莫尔斯电码(Morse Code)
   13. 编码的故事
2. 各种文本加密
3. 换位加密:
   1. 栅栏密码(Rail-fence Cipher)
   2. 曲路密码(Curve Cipher)
   3. 列移位密码(Columnar Transposition Cipher)
4. 替换加密:
   1. 埃特巴什码(Atbash Cipher)
   2. 凯撒密码(Caesar Cipher)
   3. ROT5/13/18/47
   4. 简单换位密码(Simple Substitution Cipher)
   5. 希尔密码(Hill Cipher)
   6. 猪圈密码(Pigpen Cipher)
   7. 波利比奥斯方阵密码（Polybius Square Cipher)
   8. 夏多密码(曲折加密)
   9. 普莱菲尔密码(Playfair Cipher)
   10. 维吉尼亚密码(Vigenère Cipher)
   11. 自动密钥密码(Autokey Cipher)
   12. 博福特密码(Beaufort Cipher)
   13. 滚动密钥密码(Running Key Cipher)
   14. Porta密码(Porta Cipher)
   15. 同音替换密码(Homophonic Substitution Cipher)
   16. 仿射密码(Affine Cipher)
   17. 培根密码(Baconian Cipher)
   18. ADFGX和ADFGVX密码(ADFG/VX Cipher)
   19. 双密码(Bifid Cipher)
   20. 三分密码(Trifid Cipher)
   21. 四方密码(Four-Square Cipher)
   22. 棋盘密码（Checkerboard Cipher)
   23. 跨棋盘密码(Straddle Checkerboard Cipher)
   24. 分组摩尔斯替换密码(Fractionated Morse Cipher)
   25. Bazeries密码(Bazeries Cipher)
   26. Digrafid密码(Digrafid Cipher)
   27. 格朗普雷密码(Grandpré Cipher)
   28. 比尔密码(Beale ciphers)
   29. 键盘密码(Keyboard Cipher)
5. 其他有趣的机械密码:
   1. 恩尼格玛密码
6. 代码混淆加密:
   1. asp混淆加密
   2. php混淆加密
   3. css/js混淆加密
   4. VBScript.Encode混淆加密
   5. ppencode
   6. rrencode
   7. jjencode/aaencode
   8. JSfuck
   9. jother
   10. brainfuck编程语言
7. 相关工具
8. 参考网站