# PAK

PAK(**Pa**c**k**age) 파일은 악튜러스의 데이터 파일(글꼴, 이미지 등)이 모인 압축 파일로, 자체 규격을 갖고 있습니다.

## 구조
```cpp
struct PAKHeader {
    uint32_t Offset;                ///< 오프셋
    uint32_t NumberOfEntries;       ///< 엔트리의 수
    uint8_t Magic;                  ///< (추정) 매직넘버
};

struct PAKEntry {
    std::string Name;               ///< 이름 또는 경로
    uint8_t Type;                   ///< 유형
    uint32_t Offset;                ///< 오프셋
    uint32_t CompressedSize;        ///< 압축 크기
    uint32_t UncompressedSize;      ///< 비압축 크기 (원본 크기)  
};
```

`Type`는 1 또는 3의 값이 사용되는 걸로 확인되며, 분석 정보가 백업되어 있지 않아 정확히 어떤 것을 의미하는 지 기억나지 않습니다.

### 읽기
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

PAK 파일은 용량이 크기 때문에 Memory Mapped IO(메모리 맵 입출력)를 사용해서 읽는 것이 좋습니다.