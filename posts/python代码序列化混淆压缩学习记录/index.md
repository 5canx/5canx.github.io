# Python代码序列化混淆压缩学习记录


<!--more-->

# Python代码序列化混淆压缩学习记录

## 一、了解Pyc文件

### 1.    Pyc 文件的作用：
1. 加载的速度提高、性能提高、文件

2. 保持加密的状态，防止源码泄露

3. 不同版本编译后的pyc文件是不同的，不同版本不能放在一起运行

4. Pyc文件结构：

   ![204847dsle7i1i7us4f6ie](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202405151239429.png)

### 2.    Pyc 生成的方法：
1. 自动化生成：py文件里只要包含import，就会自动生成在__pycache__目录下，含版本号

2. 使用生成函数    

``` python
py_complie.complie("demo.py") 
```

3. 文件夹下的Py文件编译为Pyc文件使用函数：
```python
compileall.compile_dir(r'路径)
```

4. 通过 Python Shell 命令生成 
 ```python
python3 -m py_compile md5.py
python3 -O -m py_compile md5.py
 ```
##  二 、混淆与反序列化Pyc文件 

### 1.    Pyc 混淆的方法：

```python
pyc = Path("__pycache__/demo.cpython-311.pyc").read_bytes()

# 显示 pyc 文件的头部信息
magic = pyc[:4]
zero = pyc[4:8]
timestamp = pyc[8:12]
size = pyc[12:16]

# 以 16 进制显示
print('pythons3.11 magic:', bytes(magic).hex(sep=' ').upper())
print('zero:', zero)
print('timestamp:', struct.unpack('<I', timestamp)[0])
print('size:', struct.unpack('<I', size)[0])

# 加载 pyc 文件中的 Python 对象
pyobj = marshal.loads(pyc[16:])

# 压缩 pyc 文件中的 Python 对象
print(zlib.compress(marshal.dumps(pyobj)))

# 执行 pyc 文件中的 Python 对象
exec(pyobj)
```
### 2.    Pyc 反序列化还原操作：

``` python
# 通过 code object 生成 pyc
HEADER = magic + b"\x00" * 4
# 时间随便写
HEADER += struct.pack("<I", int(time.time()))
# 大小随便写
HEADER += struct.pack("<I", 30)

code = HEADER + marshal.dumps('代码')

Path('test.pyc').write_bytes(code)

#生成pyc之后在终端cmd运行
.\pycdc.exe .\test.pyc
```

### 3.  使用dis模块进行指令还原代码： 

```py	
import dis
import sys

def disassemble_pyc_file(pyc_file_path):
    try:
        with open(pyc_file_path, 'rb') as file:
            magic = file.read(4)
            timestamp = file.read(4)
            size = file.read(4)
            code_object = file.read()

        print("Disassembling bytecode for:", pyc_file_path)
        dis.dis(code_object)
    except FileNotFoundError:
        print("File not found:", pyc_file_path)
    except Exception as e:
        print("An error occurred:", e)

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python disassemble_pyc.py <pyc_file>")
    else:
        pyc_file_path = sys.argv[1]
        disassemble_pyc_file(pyc_file_path)
        
        
#使用方法:   python disassemble_pyc.py <pyc_file>
```



{{< admonition type=info title="注意" open=true >}}

Pyc生成要与反编译（反序列化）的Python版本所对应

{{< /admonition >}}


---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://hack-scan.github.io/posts/python%E4%BB%A3%E7%A0%81%E5%BA%8F%E5%88%97%E5%8C%96%E6%B7%B7%E6%B7%86%E5%8E%8B%E7%BC%A9%E5%AD%A6%E4%B9%A0%E8%AE%B0%E5%BD%95/  

