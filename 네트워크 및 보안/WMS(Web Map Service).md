### **WMS(Web Map Service)란?**

- *WMS(Web Map Service)**는 OGC(Open Geospatial Consortium)에서 개발한 표준 웹 서비스로, 인터넷을 통해 **지도 이미지를 요청하고 제공하는 서비스**입니다.

주요 특징:

- **지도 이미지 제공:** GIS(Geographic Information System) 데이터를 PNG, JPEG, GIF 등의 **이미지 형식**으로 반환
- **레이어 개념 지원:** 여러 개의 지도 레이어(예: 도로, 건물, 지형 등)를 조합하여 지도 생성 가능
- **표준 프로토콜 사용:** HTTP 요청을 통해 서비스와 통신 (RESTful과 비슷한 개념)
- **좌표계 지원:** 여러 종류의 좌표계를 지원 (EPSG:4326, EPSG:3857 등)

---

### **WMS의 주요 기능**

1. **GetCapabilities**
    - 서비스의 정보를 반환 (사용 가능한 지도 레이어 목록, 지원 좌표계 등)
    - 예제 요청:
        
        ```
        
        http://example.com/wms?service=WMS&request=GetCapabilities
        
        ```
        
2. **GetMap**
    - 특정 영역의 지도 이미지를 요청
    - 요청 시 파라미터: BBOX(영역), WIDTH, HEIGHT, FORMAT, SRS(좌표계) 등
    - 예제 요청:
        
        ```
        
        http://example.com/wms?service=WMS&request=GetMap&bbox=126.8,37.2,127.2,37.6&width=800&height=600&format=image/png&srs=EPSG:4326
        
        ```
        
3. **GetFeatureInfo (선택적 기능)**
    - 지도 위 특정 지점의 속성 정보를 조회
    - WMS에서 지도 이미지를 제공하지만, 데이터 속성 정보도 요청할 수 있음
    - 예제 요청:
        
        ```
        
        http://example.com/wms?service=WMS&request=GetFeatureInfo&query_layers=roads&x=100&y=200&info_format=text/xml
        
        ```
        

---

### **WMS 구조와 작동 방식**

![image.png](attachment:54298585-442b-4edb-9e04-a0fc1bdbb861:image.png)

### **📌 WMS 동작 개요**

1️⃣ 사용자가 클라이언트(GIS 소프트웨어, 웹 지도)에서 지도 요청

2️⃣ WMS 서버가 요청된 지리 정보를 처리하여 이미지 생성

3️⃣ 생성된 이미지를 클라이언트에 반환하여 지도 표시

### **🗺 WMS 아키텍처**

- **클라이언트 (QGIS, OpenLayers, Leaflet 등)**
- **WMS 서버 (GeoServer, MapServer, ArcGIS Server 등)**
- **GIS 데이터 (Shapefile, PostGIS, Raster 데이터 등)**

---

### **한눈에 보는 WMS 동작 원리**

아래는 WMS가 어떻게 요청을 받고 응답하는지 시각적으로 표현한 이미지입니다.

🔽 **WMS 요청-응답 흐름**
1️⃣ 클라이언트가 WMS 서버에 `GetMap` 요청

2️⃣ WMS 서버가 공간 데이터에서 해당 지역을 추출하여 지도 이미지 생성

3️⃣ 클라이언트가 받은 이미지 데이터를 웹 또는 GIS 프로그램에서 표시

---

### **WMS vs WMTS vs WFS 차이점**

| 서비스 | 설명 | 반환 형식 |
| --- | --- | --- |
| **WMS** | 지도 이미지를 제공 | Raster (PNG, JPEG, GIF) |
| **WMTS** | 타일 기반으로 지도 제공 | Raster (타일 이미지) |
| **WFS** | 벡터 데이터를 제공 (좌표 정보 포함) | Vector (GeoJSON, GML, Shapefile) |

👉 **WMS는 정적인 이미지 제공**, **WFS는 실제 벡터 데이터를 전송하여 데이터 분석 가능**, **WMTS는 빠른 로딩을 위해 타일을 미리 생성**하는 방식입니다.

---

### **WMS의 활용 사례**

- **환경 모니터링:** 대기오염, 토지 이용 변화 등의 시각화
- **국토 관리:** 지형 분석, 도시 개발 계획 수립
- **재난 관리:** 홍수 예측, 산불 감시, 지진 분석 등
- **교통 시스템:** 실시간 교통 지도 및 도로 네트워크 분석

---

### **결론**

WMS는 **웹 기반 GIS 서비스의 핵심 기술**로, 다양한 지도 데이터를 효율적으로 제공하는 표준입니다. GIS 데이터를 직접 다룰 필요 없이, **서버에서 생성된 지도 이미지를 쉽게 웹에서 표시**할 수 있어 많은 분야에서 활용되고 있습니다.
