# HEX转BIN格式与HEX文件CRC校验

## HEX文件格式

| 行开始 | 长度 | 地址 | 类型 | 数据    | 校验 |
| ------ | ---- | ---- | ---- | ------- | ---- |
| :      | LL   | DDDD | TT   | DD...DD | CC   |
| 1B     | 1B   | 2B   | 1B   | nB      | 1B   |

- **行开始：**固定为`:`
- **长度：**数据字段的字节数
- **地址：**存储地址
- **类型：**
  - **00：**数据标识
  - **01：**文件结束标识
  - **02：**拓展段标识
  - **03：**起始段地址标识
  - **04**：扩展线性地址标识
  - **05：**起始线性地址标识

- **校验：**CheckSum = 0x100 - (Sum & 0xFF)

## python脚本

```python
pip install crc
```

```python
from crc import Configuration, Register

def hex_to_bin(hex_file_path: str, bin_file_path: str):
    """将 HEX 文件转换为 BIN 文件"""
    with open(hex_file_path, 'r') as hex_file, open(bin_file_path, 'wb') as bin_file:
        for line in hex_file:
            if line.startswith(':'):
                data_length = int(line[1:3], 16)  # 数据长度
                address = int(line[3:7], 16)      # 地址（可以用于其他目的，但这里不使用）
                record_type = int(line[7:9], 16)  # 记录类型
                data = line[9:9 + data_length * 2]  # 数据部分
                checksum = int(line[9 + data_length * 2:11 + data_length * 2], 16)  # 校验和
                # 只处理数据记录
                if record_type == 0:
                    # 转换十六进制数据为字节并写入 BIN 文件
                    bin_file.write(bytes.fromhex(data))

def compute_crc_for_bin_file(bin_file_path: str) -> int:
    # 创建 CRC16-CCITT 对象，使用默认参数
    config = Configuration(
        width=16,
        polynomial=0x1021,
        init_value=0x0000,
        final_xor_value=0x0000,
        reverse_input=True,
        reverse_output=True,
    )
    calculator = Register(config)
    calculator.init()
    with open(bin_file_path, 'rb') as file:
        while (data := file.read(1024)):  # 每次读取1024字节
            calculator.update(data)
    return calculator.digest()

def compute_crc_for_hex_file(hex_file_path: str) -> int:
    config = Configuration(
        width=16,
        polynomial=0x1021,
        init_value=0x0000,
        final_xor_value=0x0000,
        reverse_input=True,
        reverse_output=True,
    )
    calculator = Register(config)
    calculator.init()
    with open(hex_file_path, 'r') as hex_file:
        for line in hex_file:
            if line.startswith(':'):
                data_length = int(line[1:3], 16)  # 数据长度
                address = int(line[3:7], 16)      # 地址（可以用于其他目的，但这里不使用）
                record_type = int(line[7:9], 16)  # 记录类型
                data = line[9:9 + data_length * 2]  # 数据部分
                checksum = int(line[9 + data_length * 2:11 + data_length * 2], 16)  # 校验和
                if record_type == 0:
                    calculator.update(bytes.fromhex(data))
    return calculator.digest()

def main():
    hex_file_path = r"C:\Users\Desktop\1234.hex"  # 替换为您的 HEX 文件路径
    bin_file_path = r"C:\Users\Desktop\1234.bin"  # 输出的 BIN 文件路径
    
    # 计算hex的CRC
    crc_result = compute_crc_for_hex_file(hex_file_path)
    print(f"CRC16-CCITT 校验码: {crc_result:04X}")

    # 执行转换
    hex_to_bin(hex_file_path, bin_file_path)
    print(f"HEX 文件 {hex_file_path} 已成功转换为 BIN 文件 {bin_file_path}")

    # 计算bin的CRC
    crc_result = compute_crc_for_bin_file(bin_file_path)
    print(f"CRC16-CCITT 校验码: {crc_result:04X}")

if __name__ == "__main__":
    main()
```
