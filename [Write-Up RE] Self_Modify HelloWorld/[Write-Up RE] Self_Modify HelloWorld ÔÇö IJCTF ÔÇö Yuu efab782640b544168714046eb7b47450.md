# [Write-Up RE] Self_Modify HelloWorld — IJCTF — Yuurii

Created: November 28, 2023 2:22 PM

![Untitled](%5BWrite-Up%20RE%5D%20Self_Modify%20HelloWorld%20%E2%80%94%20IJCTF%20%E2%80%94%20Yuu%20efab782640b544168714046eb7b47450/Untitled.png)

Thông tin về chương trình như sau.

Load chương trình trong IDA ta nhận thấy nếu đi từ hàm main chương trình không khác gì chương trình “Hello world” bình thường bằng C++

![Untitled](%5BWrite-Up%20RE%5D%20Self_Modify%20HelloWorld%20%E2%80%94%20IJCTF%20%E2%80%94%20Yuu%20efab782640b544168714046eb7b47450/Untitled%201.png)

Chúng ta xem xét bắt đầu từ EP nhận thấy trong hàm init chương trình có gọi 5 lần các phần tử của “_frame_dummy_init_array_entry” để khởi tạo môi trường trước khi chạy hàm main

![Untitled](%5BWrite-Up%20RE%5D%20Self_Modify%20HelloWorld%20%E2%80%94%20IJCTF%20%E2%80%94%20Yuu%20efab782640b544168714046eb7b47450/Untitled%202.png)

Xem xét từng phần tử của “_frame_dummy_init_array_entry” nhận thấy có 3 đoạn code **x y z** khá khả nghi. Thêm vào đó trong hàm _Z1dv có gọi đến hàm “**mprotect**” để cấp full quyền đọc, ghi, thực thi cho vùng nhớ sau symbol “x” như sau:

![Untitled](%5BWrite-Up%20RE%5D%20Self_Modify%20HelloWorld%20%E2%80%94%20IJCTF%20%E2%80%94%20Yuu%20efab782640b544168714046eb7b47450/Untitled%203.png)

![Untitled](%5BWrite-Up%20RE%5D%20Self_Modify%20HelloWorld%20%E2%80%94%20IJCTF%20%E2%80%94%20Yuu%20efab782640b544168714046eb7b47450/Untitled%204.png)

Nhấn vào từng đoạn và chọn makecode có thể thấy được rằng nó đang tự động sửa code của mình  sau đó tiếp tục thực thi các đoạn code đã sửa:

![Untitled](%5BWrite-Up%20RE%5D%20Self_Modify%20HelloWorld%20%E2%80%94%20IJCTF%20%E2%80%94%20Yuu%20efab782640b544168714046eb7b47450/Untitled%205.png)

![Untitled](%5BWrite-Up%20RE%5D%20Self_Modify%20HelloWorld%20%E2%80%94%20IJCTF%20%E2%80%94%20Yuu%20efab782640b544168714046eb7b47450/Untitled%206.png)

![Untitled](%5BWrite-Up%20RE%5D%20Self_Modify%20HelloWorld%20%E2%80%94%20IJCTF%20%E2%80%94%20Yuu%20efab782640b544168714046eb7b47450/Untitled%207.png)

Ta viết một đoạn IDApython để modify code theo các đoạn mã trên tại địa chỉ của nó để nhận 3 hàm nó sẽ chạy

```jsx
import ida_bytes

start = 0x00000000005C7206
for i in range(0x399):
    tmp = ida_bytes.get_byte(start + i)
    tmp ^= 0x13
    ida_bytes.patch_byte(start + i, tmp)

start = 0x00000000005C75C6
for i in range(0x15F):
    tmp = ida_bytes.get_byte(start + i)
    tmp ^= 0x33
    ida_bytes.patch_byte(start + i, tmp)
    
start = 0x00000000005C7766
for i in range(0x0F7):
    tmp = ida_bytes.get_byte(start + i)
    tmp ^= 0x37
    ida_bytes.patch_byte(start + i, tmp)
```

Ta nhận được các hàm sau:

### Hàm tại x

```c
void sub_5C7206()
{
  char v0; // bl
  std::filesystem::__cxx11::path *v1; // rax
  int v2; // r8d
  int v3; // r9d
  char v4[16]; // [rsp+0h] [rbp-F0h] BYREF
  char v5[16]; // [rsp+10h] [rbp-E0h] BYREF
  char v6[16]; // [rsp+20h] [rbp-D0h] BYREF
  char v7[48]; // [rsp+30h] [rbp-C0h] BYREF
  char v8[16]; // [rsp+60h] [rbp-90h] BYREF
  char v9[16]; // [rsp+70h] [rbp-80h] BYREF
  char v10[32]; // [rsp+80h] [rbp-70h] BYREF
  char v11[48]; // [rsp+A0h] [rbp-50h] BYREF
  std::filesystem::__cxx11::directory_entry *v12; // [rsp+D0h] [rbp-20h]
  std::filesystem::__cxx11::directory_iterator *v13; // [rsp+D8h] [rbp-18h]

  std::filesystem::__cxx11::path::path<char [2],std::filesystem::__cxx11::path>(v7, path, 2LL);
  std::filesystem::__cxx11::directory_iterator::directory_iterator(
    (std::filesystem::__cxx11::directory_iterator *)v6,
    (const std::filesystem::__cxx11::path *)v7);
  v13 = (std::filesystem::__cxx11::directory_iterator *)v6;
  std::filesystem::__cxx11::path::~path((std::filesystem::__cxx11::path *)v7);
  std::filesystem::__cxx11::directory_iterator::directory_iterator(
    (std::filesystem::__cxx11::directory_iterator *)v8,
    v13);
  std::filesystem::__cxx11::begin(v5, v8);
  std::filesystem::__cxx11::directory_iterator::~directory_iterator((std::filesystem::__cxx11::directory_iterator *)v8);
  std::filesystem::__cxx11::directory_iterator::directory_iterator(
    (std::filesystem::__cxx11::directory_iterator *)v9,
    v13);
  std::filesystem::__cxx11::end(v4, v9);
  std::filesystem::__cxx11::directory_iterator::~directory_iterator((std::filesystem::__cxx11::directory_iterator *)v9);
  while ( (unsigned __int8)std::filesystem::__cxx11::operator!=(v5, v4) )
  {
    v12 = (std::filesystem::__cxx11::directory_entry *)std::filesystem::__cxx11::directory_iterator::operator*(v5);
    std::filesystem::__cxx11::directory_entry::path(v12);
    std::filesystem::__cxx11::path::filename((std::filesystem::__cxx11::path *)v11);
    std::filesystem::__cxx11::path::string((std::filesystem::__cxx11::path *)v10);
    v0 = std::operator==<char>(v10, fl);
    std::string::~string(v10);
    std::filesystem::__cxx11::path::~path((std::filesystem::__cxx11::path *)v11);
    if ( v0 )
    {
      v1 = (std::filesystem::__cxx11::path *)std::filesystem::__cxx11::directory_entry::path(v12);
      filename = std::filesystem::__cxx11::path::c_str(v1);
      fptr[0] = fopen64(filename, mode1);
      _isoc99_fscanf(fptr[0], (unsigned int)format, (unsigned int)lel, (unsigned int)_isoc99_fscanf, v2, v3, v4[0]);
      fclose(fptr[0]);
    }
    std::filesystem::__cxx11::directory_iterator::operator++(v5);
  }
  std::filesystem::__cxx11::directory_iterator::~directory_iterator((std::filesystem::__cxx11::directory_iterator *)v4);
  std::filesystem::__cxx11::directory_iterator::~directory_iterator((std::filesystem::__cxx11::directory_iterator *)v5);
  std::filesystem::__cxx11::directory_iterator::~directory_iterator((std::filesystem::__cxx11::directory_iterator *)v6);
}
```

Hàm này cơ bản sẽ scan toàn bộ file trong thư mục và tìm file có tên là “flag.txt” để đọc nội dung vào “lel”

### Hàm tại y

```c
__int64 sub_5C75C6()
{
  __int64 result; // rax
  char v1; // al
  char v2; // al
  char v3; // al
  char v4; // al
  char v5; // al
  char v6; // [rsp+0h] [rbp-Ah]
  char v7; // [rsp+1h] [rbp-9h]
  char v8; // [rsp+1h] [rbp-9h]
  int i; // [rsp+6h] [rbp-4h]

  for ( i = 0; ; i += 2 )
  {
    result = (unsigned int)i;
    if ( i >= 100 )
      break;
    v7 = ((unsigned __int8)lel[i] >> 4) + 16 * lel[i + 1];
    v6 = ((unsigned __int8)lel[i + 1] >> 4) + 16 * lel[i];
    if ( v7 >= 0 )
      v1 = 2 * v7;
    else
      v1 = (2 * v7) | 1;
    if ( v1 >= 0 )
      v2 = 2 * v1;
    else
      v2 = (2 * v1) | 1;
    if ( v2 >= 0 )
      v3 = 2 * v2;
    else
      v3 = (2 * v2) | 1;
    v8 = v3;
    if ( v6 >= 0 )
      v4 = 2 * v6;
    else
      v4 = (2 * v6) | 1;
    if ( v4 >= 0 )
      v5 = 2 * v4;
    else
      v5 = (2 * v4) | 1;
    lel[i] = v8 ^ 0x13;
    lel[i + 1] = v5 ^ 0x37;
  }
  return result;
}
```

Hàm này sẽ lấy 2 ký tự 1 của dãy đọc được đảo 4 bit cuối của ký tự thứ 2 thành 4 bít đầu của ký tự thứ nhất và chuyển 4 bit đầu của ký tự thứ nhất thành 4 bit cuối của nó, đổi 4 bit cuối của ký tự thứ nhất thành 4 bit đầu của ký tự thứ 2 và chuyển 4 bit đầu của ký tự thứ 2 thành 4 bit cuối của nó.

Cơ bản như sau: 

 

```c
0x34 và 0x56 => 0x63 và 0x45
```

Sau đó nó tiến hành xoay trái 3 bit cho mỗi ký tự và xor với hằng số 0x13 và 0x37 

### Hàm tại z

```c
char sub_5C7766()
{
  char result; // al
  __int64 v1; // rax
  int v2; // r8d
  int v3; // r9d

  std::string::operator=(&enc[abi:cxx11], fl);
  result = (unsigned int)remove(fl) == 0;
  if ( result )
  {
    std::string::operator+=(&enc[abi:cxx11], encr);
    v1 = std::string::c_str(&enc[abi:cxx11]);
    fptr[0] = fopen64(v1, mode2);
    fprintf(fptr[0], (unsigned int)format, (unsigned int)lel, (unsigned int)fprintf, v2, v3);
    return fclose(fptr[0]);
  }
  return result;
}
```

Hàm này sẽ ghi cipher_text ra file “flag.txt.enc” sau đó xóa file “flag.txt”

## Solve

Ta giải mã các byte trong “flag.txt.enc” bằng mã sau:

```c
#include <stdio.h>
#include <malloc.h>

void rotate_right_bit(unsigned __int8 *num, int ror_bits)
{
    *num = ((*num >> ror_bits) | (*num << (8 - ror_bits))) & ((1 << 8) - 1);
}

void decrypt_cipher(unsigned __int8 *ct)
{
    unsigned __int8 v1;
    unsigned __int8 v2;
    unsigned __int8 v3;
    unsigned __int8 v4;

    for (size_t i = 0; i < 100; i += 2)
    {
        unsigned int result = (unsigned int)i;
        unsigned __int8 v1 = *(ct + i);
        unsigned __int8 v2 = *(ct + i + 1);
        v1 ^= 0x13;
        v2 ^= 0x37;
        rotate_right_bit(&v1, 3);
        rotate_right_bit(&v2, 2);
        unsigned __int8 v3 = (v1 << 4) + (v2 >> 4);
        unsigned __int8 v4 = (v2 << 4) + (v1 >> 4);
        *(ct + i) = v3;
        *(ct + i + 1) = v4;
    }
}

int main(int argc, char const *argv[])
{
    FILE *flag_enc = fopen("flag.txt.enc", "rb");
    unsigned __int8 *cipher_text = (unsigned __int8 *)calloc(100, sizeof(unsigned __int8));

    size_t lenght_cipher = fread(cipher_text, sizeof(unsigned __int8), 1000, flag_enc);
    printf("--Cipher--\n");
    for (size_t i = 0; i < lenght_cipher; i++)
    {
        printf("%x\t", *(cipher_text + i));
    }
    printf("\n--Plain--\n");
    decrypt_cipher(cipher_text);
    for (size_t i = 0; i < lenght_cipher; i++)
    {
        printf("%c", *(cipher_text + i));
    }
}
```

## FLAG: IJCTF{fb1551bdd947185c4c5027da19c82205}

—ckagngoc—