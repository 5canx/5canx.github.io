# IDA逆向作业练习（todo_0）

<!--more-->

## IDA逆向作业练习

打开后搜索todo_0方法，找到后按Tab进入混淆汇编代码，美化前：

![](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202505271949479.png)

代码分析得知：

strlen_chk方法有a1参数，所以他可能为输入参数，则赋予v2变量可能为字符串长度，v9可能为申请内存后的地址，大小为v2+1。 strcpy_chk可能为复制操作，将a1复制到v9，运行sub_208BC方法后，释放内存。

接着进入sub_208BC函数：

```bash
__int64 __usercall sub_208BC@<X0>(__int64 a1@<X0>, unsigned __int64 a2@<X1>, __int64 a3@<X8>)
{
  __int64 result; // x0
  unsigned __int64 i; // [xsp+18h] [xbp-28h]
	//unsigned  无符号
  result = sub_21BF4();
  for ( i = 0LL; i < a2; ++i )
  {
    sub_23140(a3, (unsigned __int8)a0123456789abcd[(int)*(unsigned __int8 *)(a1 + i) >> 4]);
    //sub_23140方法将a1 + i的值右移四位
    result = sub_23140(a3, (unsigned __int8)a0123456789abcd[*(_BYTE *)(a1 + i) & 0xF]);
  }
  return result;
}
```

美化后：

```bash
void __usercall todo_0_1(char *input_str_2@<X0>, char *output_str@<X8>)
{
  size_t input_str; // x0
  char v3; // [xsp+14h] [xbp-7Ch]
  int j; // [xsp+2Ch] [xbp-64h]
  int i; // [xsp+30h] [xbp-60h]
  int random_num; // [xsp+34h] [xbp-5Ch]
  size_t input_len_1; // [xsp+38h] [xbp-58h]
  _BYTE *data; // [xsp+40h] [xbp-50h]

  input_str = __strlen_chk(input_str_2, 0xFFFFFFFFFFFFFFFFLL);
  data = operator new[](input_str + 1);
  input_len_1 = __strlen_chk(input_str_2, 0xFFFFFFFFFFFFFFFFLL);
  __strcpy_chk(data, input_str_2, -1LL);
  srand(input_len_1);
  random_num = rand() & 0xF;
  for ( i = 0; i < random_num; ++i )
  {
    for ( j = 0; j < input_len_1; ++j )
    {
      v3 = data[j];
      data[j] = v3 ^ rand();
    }
  }
  enccode_hex(data, input_len_1, output_str);
  if ( data )
    operator delete[](data);
}
```

还原代码：

```python
from ctypes import c_int
import binascii

# Windows随机数生成器
windows_status = 1

def windows_srand(seed):
    global windows_status
    windows_status = seed

def windows_rand():
    global windows_status
    windows_status = (214013 * windows_status + 2531011) & 0xffffffff
    return windows_status >> 16 & ((1 << 15) - 1)

# Android随机数生成器
android_seed = 0
android_multiplier = 0x5DEECE66D
android_addend = 0xB
android_mask = (1 << 48) - 1
android_seedUniquifier = 8682522807148012

def android_srand(seed):
    global android_seed
    android_seed = _initialScramble(seed)

def _initialScramble(seed):
    return (seed ^ android_multiplier) & android_mask

def _next(bits):
    global android_seed
    oldseed = android_seed
    nextseed = (oldseed * android_multiplier + android_addend) & android_mask
    android_seed = nextseed
    return c_int(((nextseed >> (48 - bits)))).value

def android_nextInt():
    return _next(32)

def android_nextInt_bound(bound):
    global android_seed
    r = _next(31)
    m = bound - 1
    if bound & m == 0:
        r = (((bound * (r & 0xffffffffffffffff)) >> 31) & 0xffffffff)
    else:
        u = r
        r = u % bound
        while u - r + m < 0:
            r = u % bound
            u = _next(31)
    return r

# Linux随机数生成器
linux_status = 0
linux_r = []

def linux_srand(seed):
    global linux_status, linux_r
    if seed == 0:
        seed = 1
    word = seed
    seed = seed & 0xffffffff
    linux_status = 0
    linux_r = [0] * (344 + linux_status)
    linux_r[0] = seed
    for i in range(1, 31):
        if word < 31:
            hi = (-word) // 127773
            hi = -hi
            lo = (-word) % 127773
            lo = -lo
        else:
            hi = word // 127773
            lo = word % 127773
        word = ((16807 * lo)) - ((2836 * hi))
        if word < 0:
            word = (2147483647 + word) & 0xffffffff
        linux_r[i] = word
    for i in range(31, 34):
        linux_r[i] = linux_r[i - 31]
    for i in range(34, 344):
        linux_r[i] = (((linux_r[i - 31] + linux_r[i - 3]) & 0xffffffff) % (1 << 32)) & 0xffffffff

def linux_rand():
    global linux_status, linux_r
    linux_r.append(0)
    linux_r[344 + linux_status] = (((linux_r[344 + linux_status - 31] + linux_r[344 + linux_status - 3]) & 0xffffffff) % (1 << 32)) & 0xffffffff
    linux_status += 1
    return linux_r[344 + linux_status - 1] >> 1

# 加密函数
def todo_0(s):
    linux_srand(len(s))
    enc_loop = linux_rand() & 0xF
    s = bytearray(s)
    for _ in range(enc_loop):
        for i in range(len(s)):
            s[i] = s[i] ^ (linux_rand() & 0xff)  #0xff是限制结果
    return s

# 示例使用
input_bytes = b'1234'
encrypted_bytes = todo_0(input_bytes)
print(binascii.hexlify(encrypted_bytes))
```



---

> 作者: [Scan](https://www.scan.work/)  
> URL: http://localhost:1313/posts/ida%E9%80%86%E5%90%91%E4%BD%9C%E4%B8%9A%E7%BB%83%E4%B9%A0/  

