	模块：
	idc  封装IDA的IDC函数的兼容性模块
	idautils   高级实用功能模块
	idaapi   访问更加底层的数据

[hex-rays.com/products/ida/support/idapython_docs/](https://www.hex-rays.com/products/ida/support/idapython_docs/)

```
获取光标位置
 ea = idc.get_screen_ea()
 ea = here()


hex(idc.get_inf_attr(INF_MIN_EA)) # 节中起始地址地
hex(idc.get_inf_attr(INF_MAX_EA)) # 节中结束地址


idc.get_segm_name(ea) # 获取所在的区段名称字符串
idc.generate_disasm_line(ea,0) # 获取光标所在行的反汇编指令
idc.print_insn_mnem(ea) # 获取命令
idc.print_operand(ea,0) # 获取第一个操作数 
idc.print_operand(ea,1) # 获取第二个操作数


idautils.Segments() # 返回的是一个可以迭代的对象，使用for循环来遍历它，
idc.get_segm_name(seg) # 获取迭代对象所在段的名称字符串，
idc.get_segm_start(seg) idc.get_segm_end(seg) # 获取区段开始和结束位置的地址
idc.get_next_seg(ea) # 找到光标的下一个区段

例：
for seg in idautils.Segments():                                                   
	print(idc.get_segm_name(seg),idc.get_segm_start(seg),idc.get_segm_end(seg))
```

```
idautils.Functions(start_addr, end_addr) 返回已知函数信息，该信息包含每个函数的起始地址和函数名，可以用for进行遍历,参数为开始和结束地址（可省略）

idc.get_func_name(ea) # 获得函数名

例：
for func in idautils.Functions(): 
	print(hex(func), idc.get_func_name(func))
```


# idc库
```
1. 获取函数信息：
    
    - `get_func_name(ea)`: 获取指定地址的函数名称。
    - `get_func_offset(ea)`: 获取指定地址相对于所属函数的偏移量。
    - `get_func_attr(ea, attr)`: 获取指定地址函数的属性，例如函数类型、函数调用约定等。
    - `get_func_cmt(ea, repeatable=False)`: 获取指定地址函数的注释。
    - `get_func_args_size(ea)`: 获取指定地址函数的参数大小。
2. 获取指令信息：
    
    - `get_disasm(ea)`: 获取指定地址的反汇编字符串。
    - `get_operand_value(ea, op)`: 获取指定地址指令的操作数值。
    - `get_operand_type(ea, op)`: 获取指定地址指令的操作数类型。
    - `is_call_insn(ea)`: 检查指定地址是否是函数调用指令。
3. 数据类型和结构：
    
    - `get_bytes(ea, size)`: 以字节形式获取指定地址开始的数据。
    - `get_wide_dword(ea)`: 获取指定地址的4字节（32位）宽度的值。
    - `get_qword(ea)`: 获取指定地址的8字节（64位）宽度的值。
    - `get_strlit_contents(ea, length=-1, strtype=0)`: 获取指定地址的字符串内容。
    - `get_name(ea)`: 获取指定地址的名称。
4. 导航和分析：
    
    - `next_head(ea)`: 获取指定地址的下一条指令地址。
    - `prev_head(ea)`: 获取指定地址的上一条指令地址。
    - `find_text(start_ea, end_ea, search_text, flags=0)`: 在指定地址范围内搜索文本。
    - `get_func_qty()`: 获取函数的数量。
    - `get_first_func()`: 获取第一个函数的起始地址。
```



# idautils库
```
1. 遍历函数和指令：
    
    - `Functions(start=None, end=None)`: 获取函数地址的生成器。
    - `FuncItems(func_ea)`: 获取函数指令地址的生成器。
    - `Heads(start=None, end=None)`: 获取指令地址的生成器。
2. 符号和命名：
    
    - `Symbols()`: 获取全局符号的生成器。
    - `Names(start=None, end=None)`: 获取地址范围内的命名地址的生成器。
    - `get_name_ea_simple(name)`: 根据名称获取地址。
    - `get_func_name(ea)`: 获取指定地址的函数名称。
3. 数据交互和引用：
    
    - `DataRefsFrom(ea)`: 获取从指定地址引用的数据地址的生成器。
    - `DataRefsTo(ea)`: 获取引用到指定地址的数据地址的生成器。
    - `CodeRefsFrom(ea, flow=True)`: 获取从指定地址引用的代码地址的生成器。
    - `CodeRefsTo(ea, flow=True)`: 获取引用到指定地址的代码地址的生成器。
4. 类型和结构：
    
    - `get_strlit_contents(ea, length=-1, strtype=0)`: 获取指定地址的字符串内容。
    - `is_code(ea)`: 检查指定地址是否包含有效的代码。
    - `is_data(ea)`: 检查指定地址是否包含数据。
5. 块和段：
    
    - `Segments()`: 获取程序的段列表。
    - `get_segm_name(ea)`: 获取指定地址所在段的名称。
    - `get_func_seg(ea)`: 获取指定地址所属函数的段。
```


# idaapi库
```
1. 获取程序和数据库信息：
    
    - `get_root_filename()`: 获取当前IDA数据库的文件名。
    - `get_input_file_path()`: 获取当前IDA数据库的完整路径。
    - `get_inf_structure()`: 获取IDA数据库的信息结构。
2. 获取函数和指令信息：
    
    - `get_func(ea)`: 获取指定地址所在的函数对象。
    - `get_func_name(ea)`: 获取指定地址的函数名称。
    - `decode_insn(ea)`: 解码并返回指定地址的指令对象。
    - `is_call_insn(ea)`: 检查指定地址是否是函数调用指令。
    - `get_operand_value(insn, op)`: 获取指定指令的操作数值。
    - `get_operand_type(insn, op)`: 获取指定指令的操作数类型。
    - get_func_args(func_ea)：获取函数在给定地址func_ea处的参数列表。它返回一个包含参数名称和类型的列表。 
    - get_func_type(func_ea)：获取函数在给定地址func_ea处的函数类型信息。可以使用该函数获取更详细的函数参数信息，例如参数类型、返回类型等。 
    - get_func_cmt(func_ea, repeatable=False)：获取函数在给定地址func_ea处的注释。注释中可能包含关于函数参数的信息。
3. 符号和命名：
    
    - `get_name_ea_simple(name)`: 根据名称获取地址。
    - `get_func_offset(ea)`: 获取指定地址相对于所属函数的偏移量。
    - `get_segm_name(ea)`: 获取指定地址所在段的名称。
4. 数据类型和结构：
    
    - `get_bytes(ea, size)`: 以字节形式获取指定地址开始的数据。
    - `get_strlit_contents(ea, length=-1, strtype=0)`: 获取指定地址的字符串内容。
    - `get_tinfo(ea)`: 获取指定地址的类型信息对象。
5. 导航和分析：
    
    - `next_head(ea, maxea)`: 获取指定地址的下一条指令地址。
    - `prev_head(ea, minea)`: 获取指定地址的上一条指令地址。
    - `get_screen_ea()`: 获取当前显示的地址。
    - `get_func_qty()`: 获取函数的数量。
    - `get_first_func()`: 获取第一个函数的起始地址。
6. 反汇编和汇编：
    
    - `generate_disasm_line(ea, flags=0)`: 生成指定地址的反汇编字符串。
    - `assemble(ea, cs, ip, use32, line)`: 将指定汇编字符串汇编成机器码。

idaapi.msg_clear() 清空输出窗口（ctrl+x）
```