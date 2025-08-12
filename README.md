# arcturus_format

## EXE
|주소|기본값|변경값|
|:----|:----|:----|
|0x1A176D|B9 3C 00 00 00|B9 **3C** 00 00 00|

프레임 속도를 지정하는 함수로, 0x3C는 60의 값을 의미하고, 60프레임으로 작동되는 것을 의미합니다. 0x3C를 0x90으로 변경해 144fps가 되지만, 별도의 보간 처리가 안 되어 있어서 프레임을 증가시킨만큼 게임의 속도도 배수만큼 증가합니다.


## PAK

**추가 예정**

추정 압축 기법: LZSS

```cpp
struct PAKHeader {
	uint32_t Offset;
	uint32_t NumberOfEntries;
	uint8_t Magic;
};

struct PAKEntry {
	std::string Name;
	uint8_t Type;
	uint32_t Offset;
	uint32_t CompressedSize;
	uint32_t UncompressedSize;
};
```

```cpp
size_t pos = m_Header.Offset;
for (auto i = 0; i < m_Header.NumberOfEntries; ++i) {
	uint8_t name_length = m_Data[pos]; pos += 1;
	uint8_t type = m_Data[pos]; pos += 1;
	uint32_t offset = *reinterpret_cast<uint32_t*>(&m_Data[pos]); pos += 4;
	uint32_t compressed_size = *reinterpret_cast<uint32_t*>(&m_Data[pos]); pos += 4;
	uint32_t uncompressed_size = *reinterpret_cast<uint32_t*>(&m_Data[pos]); pos += 4;

	// Read the name
	std::string name(reinterpret_cast<const char*>(&m_Data[pos]), name_length + 1);
	pos += name_length + 1;

	// Add the entry
	m_Entries.push_back({ name.c_str(), type, offset, compressed_size, uncompressed_size });
}
```
