# arcturus_format

## PAK

**추가 예정**

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
