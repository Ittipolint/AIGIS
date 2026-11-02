# AIGIS: AI-driven Integrated Geospatial & Inundation System
**Software Requirement Specification (SRS)**

---

## 1. ภาพรวมของระบบ (Project Overview)
**AIGIS (AI-driven Integrated Geospatial & Inundation System)** คือแพลตฟอร์มอัจฉริยะด้านภูมิสารสนเทศและการบริหารจัดการอุทกภัยแบบครบวงจร ระบบทำการบูรณาการข้อมูลจากดาวเทียมสำรวจโลก ข้อมูลสภาพอากาศ แผนที่ฐาน และข้อมูลตรวจวัดภาคสนาม เข้ากับขุมพลังของ **Large Language Models (LLM)** ที่เชื่อมต่อผ่าน **Model Context Protocol (MCP)** และ **Geospatial Foundation Models (GFMs)** เพื่อช่วยในการคาดการณ์จำลองสถานการณ์น้ำท่วมล่วงหน้า (Proactive) และการวางแผนระบายน้ำรวมถึงการกู้ภัยขณะเกิดเหตุ (Reactive) ผ่านการสั่งงานด้วยภาษาธรรมชาติ (Natural Language Interface)

### 1.1 วัตถุประสงค์ (Objectives)
- เพื่อรวมศูนย์ข้อมูลดาวเทียมและข้อมูลภูมิสารสนเทศจากแหล่งข้อมูลเปิด (Open Data) และข้อมูลท้องถิ่นเข้าด้วยกันแบบอัตโนมัติ
- เพื่อให้นักวิเคราะห์ เจ้าหน้าที่รัฐ และผู้บริหาร สามารถสั่งการและสอบถามข้อมูลเชิงพื้นที่ที่ซับซ้อนผ่านการสนทนาภาษาธรรมชาติ (Chatbot/Agent)
- เพื่อจำลองและคาดการณ์พื้นที่เสี่ยงอุทกภัยล่วงหน้า พร้อมเสนอแนะแนวทางการเตรียมพร่องน้ำและการขุดลอกคูคลอง
- เพื่อติดตามขอบเขตน้ำท่วมแบบเรียลไทม์ด้วยภาพถ่ายดาวเทียมเรดาร์ (SAR) และวางแผนการระบายน้ำรวมถึงเส้นทางอพยพฉุกเฉิน

---

## 2. สถาปัตยกรรมระบบ (System Architecture)

ระบบแบ่งการทำงานออกเป็น 5 ชั้น (Layers) หลัก ดังนี้:

1. **Data Sources Layer (ชั้นแหล่งข้อมูล):**
   - **ข้อมูลดาวเทียม:** Copernicus (Sentinel-1 SAR, Sentinel-2 Optical, Sentinel-3), APSCO DSSP, USGS EarthExplorer / NASA LP DAAC (Landsat, SRTM/ALOS DEM)
   - **ข้อมูลสภาพอากาศและภูมิอากาศ:** Copernicus ERA5 (Climate Data Store), Open-Meteo API, เรดาร์ตรวจอากาศท้องถิ่น
   - **ข้อมูลแผนที่และโครงสร้างพื้นฐาน:** Google Maps Platform (Base Map, Routing, Places), OpenStreetMap (OSM), UN OCHA HDX (ขอบเขตการปกครองและข้อมูลมนุษยธรรม)
   - **ข้อมูลภาคสนามแบบเรียลไทม์ (IoT & Ground Truth):** สถานีวัดระดับน้ำ (Telemetering), สถานีวัดน้ำฝน, กล้อง CCTV, ข้อมูลรายงานจากภาคประชาชน (Crowdsourcing)

2. **Data Management & MCP Layer (ชั้นการจัดการข้อมูลและตัวเชื่อมต่อ):**
   - **MCP Servers:** ตัวกลางมาตรฐานสำหรับให้ LLM เรียกใช้งานเครื่องมือและดึงข้อมูลจาก CDSE (Copernicus), CDS, OSM, Weather APIs และ Google Maps API
   - **Data Ingestion & Preprocessing:** ระบบดึงข้อมูล ทำความสะอาด แปลงพิกัดภูมิศาสตร์ (CRS Alignment) และจัดทำ Cloud-Optimized GeoTIFF (COG)
   - **Geospatial Database:** PostgreSQL + PostGIS, Vector Database และ SpatioTemporal Asset Catalog (STAC)

3. **AI & Analytics Layer (ชั้นปัญญาประดิษฐ์และโมเดลวิเคราะห์):**
   - **LLM + Agentic Workflow:** ประมวลผลคำสั่งภาษาธรรมชาติ วางแผนการเรียกใช้เครื่องมือผ่าน MCP และสรุปรายงาน
   - **Geospatial Foundation Models (GFMs):** ใช้โมเดลเช่น Prithvi (NASA/IBM), TerraMind หรือ Tessera ในการสกัดข้อมูลพื้นที่น้ำท่วม (Flood Segmentation) และการจำแนกการใช้ประโยชน์ที่ดิน (Land Cover)
   - **Hydrological & Flood Modeling Engine:** โมเดลคำนวณทิศทางการไหลของน้ำ (Flow Direction/Accumulation), ปริมาณน้ำหลาก (Runoff) และจำลองการระบายน้ำ

4. **Application Layer (ชั้นแอปพลิเคชันและฟีเจอร์ใช้งาน):**
   - โมดูลเตรียมการก่อนเกิดภัย (Proactive Preparedness)
   - โมดูลบริหารจัดการขณะเกิดภัย (Reactive Emergency Response)

5. **Presentation / Output Layer (ชั้นการแสดงผล):**
   - Interactive 2D/3D Web GIS Map, Conversational AI Interface, ระบบ Dashboard รายงานผล และระบบแจ้งเตือนภัย (Multi-channel Alerts)

---

## 3. ความต้องการเชิงฟังก์ชัน (Functional Requirements - FR)

### FR-01: ระบบสั่งงานและวิเคราะห์ด้วยภาษาธรรมชาติ (Conversational AI & MCP Integration)
- **FR-01.1:** ผู้ใช้สามารถพิมพ์คำสั่งด้วยภาษาธรรมชาติ (ภาษาไทยและภาษาอังกฤษ) เพื่อค้นหาข้อมูลดาวเทียม สภาพอากาศ และแผนที่ได้ (เช่น *"ขอดูพื้นที่เสี่ยงน้ำท่วมในจังหวัดพระนครศรีอยุธยา หากมีฝนตกหนัก 150 มม. ใน 24 ชั่วโมงข้างหน้า"*)
- **FR-01.2:** LLM ต้องสามารถเรียกใช้งาน MCP Server ต่างๆ แบบอัตโนมัติ (Tool Calling) เพื่อดึงข้อมูลจริงมาประกอบการวิเคราะห์และวาดผลลัพธ์ลงบนแผนที่

### FR-02: ระบบบูรณาการข้อมูลหลายแหล่ง (Multi-Source Data Fusion)
- **FR-02.1:** ระบบสามารถดึงภาพถ่ายดาวเทียม Sentinel-1 (SAR) และ Sentinel-2 (Optical) ล่าสุดผ่าน Copernicus MCP Server
- **FR-02.2:** ระบบสามารถดึงข้อมูลความสูงภูมิประเทศ (DEM) และข้อมูลโครงข่ายถนน/ลำน้ำจาก OpenStreetMap และ Google Maps
- **FR-02.3:** ระบบสามารถดึงข้อมูลพยากรณ์อากาศล่วงหน้าและค่าความชื้นในดิน (Soil Moisture) แบบอัตโนมัติ

### FR-03: โมดูลการเตรียมการก่อนเกิดอุทกภัย (Proactive Flood Preparedness)
- **FR-03.1 การจำลองเส้นทางน้ำไหล (Flow Accumulation & Direction):** วิเคราะห์ข้อมูล DEM เพื่อระบุทิศทางการไหลของน้ำและจุดแอ่งกระทะที่มีความเสี่ยงน้ำท่วมขัง
- **FR-03.2 การประเมินความอิ่มตัวของดินและปริมาณน้ำหลาก:** คำนวณความสามารถในการซับน้ำของดินจากข้อมูลความชื้นในดินร่วมกับปริมาณฝนพยากรณ์
- **FR-03.3 การจำลองสถานการณ์น้ำท่วม (What-If Flood Simulation):** จำลองระดับน้ำท่วมขังภายใต้ปริมาณน้ำฝนระดับต่างๆ (เช่น ฝน 50 มม., 100 มม., 200 มม.)
- **FR-03.4 การวางแผนพร่องน้ำและบำรุงรักษา:** แนะนำจุดที่ควรขุดลอกคูคลอง กำจัดสิ่งกีดขวางทางน้ำ หรือระดับการพร่องน้ำในอ่างเก็บน้ำ/แก้มลิงล่วงหน้า

### FR-04: โมดูลการบริหารจัดการขณะเกิดอุทกภัย (Reactive Flood Management)
- **FR-04.1 การตรวจจับพื้นที่น้ำท่วมแบบเรียลไทม์ด้วย AI:** ใช้ภาพเรดาร์ SAR (ซึ่งทะลุเมฆได้ในขณะพายุเข้า) ประมวลผลผ่านโมเดล GFM เพื่อแยกแยะขอบเขตพื้นที่น้ำท่วมจริงรายวัน/รายชั่วโมง
- **FR-04.2 การประเมินผลกระทบต่อโครงสร้างพื้นฐาน:** วิเคราะห์การซ้อนทับ (Spatial Overlay) ระหว่างพื้นที่น้ำท่วมกับข้อมูลถนน โรงพยาบาล โรงเรียน และชุมชน เพื่อระบุจุดวิกฤตที่ถูกตัดขาด
- **FR-04.3 ระบบแนะนำการระบายน้ำอัจฉริยะ (Smart Drainage Optimization):** แนะนำลำดับการเปิด-ปิดประตูระบายน้ำ และการเดินเครื่องสูบน้ำ เพื่อระบายน้ำออกจากพื้นที่เศรษฐกิจหรือชุมชนลงสู่แม่น้ำสายหลักให้เร็วที่สุดโดยไม่กระทบพื้นที่ปลายน้ำเกินเกณฑ์
- **FR-04.4 การวางแผนเส้นทางฉุกเฉินและอพยพ (Emergency Routing):** เชื่อมต่อ Google Maps Routing API โดยหักลบเส้นทางที่ถูกน้ำท่วมออก เพื่อแนะนำเส้นทางอพยพหรือเส้นทางลำเลียงความช่วยเหลือที่ปลอดภัยที่สุด

### FR-05: ระบบการแสดงผลและแจ้งเตือน (Visualization & Alerting)
- **FR-05.1 Interactive GIS Dashboard:** แสดงแผนที่หลายเลเยอร์ (Multi-layer Map) ที่ผู้ใช้สามารถเปิด/ปิดเลเยอร์ภาพดาวเทียม ทิศทางน้ำไหล ขอบเขตน้ำท่วม และตำแหน่งสถานีวัดน้ำได้
- **FR-05.2 Automated Report Generation:** สร้างสรุปรายงานสถานการณ์สำหรับผู้บริหารพร้อมกราฟและแผนที่โดยอัตโนมัติ
- **FR-05.3 Real-time Alerting:** ส่งการแจ้งเตือนผ่าน API, LINE OA, SMS หรือ Web Push เมื่อระดับน้ำหรือค่าพยากรณ์เกินเกณฑ์วิกฤต (Threshold)

---

## 4. ความต้องการด้านข้อมูลและตัวเชื่อมต่อ (Data & MCP Interfaces)

| แหล่งข้อมูล (Data Source) | ประเภทข้อมูล | วิธีการเชื่อมต่อ (Integration Method) |
| :--- | :--- | :--- |
| **Copernicus (CDSE & CDS)** | Sentinel-1/2/3, ERA5 Climate, Soil Moisture | Copernicus MCP Server / STAC API / openEO |
| **APSCO DSSP** | ข้อมูลดาวเทียมพันธมิตรเอเชีย-แปซิฟิกเพื่อภัยพิบัติ | REST API / Custom MCP Connector |
| **Google Maps Platform** | Base Map, Places, Street View, Traffic & Routing | Google Maps API / MCP Server |
| **OpenStreetMap (OSM)** | โครงข่ายถนน อาคาร ลำน้ำ โครงสร้างพื้นฐาน | Overpass API / OSM MCP Server |
| **USGS / NASA LP DAAC** | DEM (SRTM, ALOS), Landsat | EarthExplorer API / STAC |
| **Open-Meteo** | พยากรณ์อากาศรายชั่วโมง ปริมาณฝน ลม | Open-Meteo REST API / Weather MCP |
| **UN OCHA HDX** | ขอบเขตการปกครอง (ADM), ข้อมูลประชากรและกลุ่มเปราะบาง | HDX HAPI / CKAN API |
| **Local IoT / ThaiWater** | ระดับน้ำในคลอง ปริมาณฝนจริง สถานะประตูระบายน้ำ | Webhook / REST API / MQTT |

---

## 5. ความต้องการที่ไม่ใช่เชิงฟังก์ชัน (Non-Functional Requirements - NFR)

- **NFR-01 ด้านประสิทธิภาพ (Performance):**
  - การตอบกลับของ LLM เบื้องต้นต้องใช้เวลาไม่เกิน 3-5 วินาที
  - การเรนเดอร์แผนที่ความละเอียดสูง (Tile Rendering) ต้องโหลดเสร็จภายใน 2 วินาทีผ่านโครงสร้าง Cloud-Optimized GeoTIFF (COG)
- **NFR-02 ด้านความสามารถในการขยายตัว (Scalability):**
  - สถาปัตยกรรมต้องรองรับการทำงานแบบ Microservices และ Containerization (Docker/Kubernetes) เพื่อขยายทรัพยากรการประมวลผล AI บนคลาวด์ได้อัตโนมัติเมื่อเกิดเหตุภัยพิบัติใหญ่
- **NFR-03 ด้านความเข้ากันได้และมาตรฐาน (Interoperability):**
  - ข้อมูลภูมิสารสนเทศทั้งหมดต้องเป็นไปตามมาตรฐาน **OGC (Open Geospatial Consortium)** เช่น WMS, WFS, GeoJSON, COG และ STAC
  - การเชื่อมต่อเครื่องมือ AI ใช้มาตรฐาน **Model Context Protocol (MCP)** เพื่อให้สามารถสลับเปลี่ยนโมเดล LLM ได้อย่างอิสระ
- **NFR-04 ด้านความมั่นคงปลอดภัย (Security & Privacy):**
  - มีการจัดการสิทธิ์การเข้าถึงข้อมูล (Role-Based Access Control - RBAC) สำหรับประชาชนทั่วไป เจ้าหน้าที่ปฏิบัติการ และผู้บริหารที่มีสิทธิ์สั่งการระบบระบายน้ำ

---

## 6. เทคโนโลยีที่แนะนำในการพัฒนา (Proposed Tech Stack)

- **Frontend:** Next.js (React), Tailwind CSS, Mapbox GL JS / Deck.gl / Leaflet, Google Maps JS SDK
- **Backend & API:** Python (FastAPI), Node.js
- **AI & Agent Orchestration:** Model Context Protocol (MCP) SDK, LangChain / LlamaIndex, Claude / Gemini / OpenAI APIs
- **Geospatial AI & Processing:** PyTorch, TorchGeo, Prithvi / TerraMind (Hugging Face), GDAL, Rasterio, GeoPandas, WhiteboxTools (สำหรับ Hydrological Modeling)
- **Database & Storage:** PostgreSQL + PostGIS, pgvector, AWS S3 / MinIO (สำหรับเก็บไฟล์ COG/STAC)

---

## 7. แผนการพัฒนาโครงการ (Development Roadmap)

- **Phase 1: Foundation & MCP Integration (เดือนที่ 1-3)**
  - พัฒนาและเชื่อมต่อ MCP Servers สำหรับ Copernicus, Open-Meteo, OSM และ Google Maps
  - สร้างหน้าจอ Chat Interface คู่กับแผนที่พื้นฐาน (Map UI)
- **Phase 2: Proactive Hydrological Modeling (เดือนที่ 4-6)**
  - พัฒนาโมดูลวิเคราะห์ DEM ทิศทางน้ำไหล และการประเมินความชื้นในดิน
  - สร้างฟีเจอร์จำลองสถานการณ์น้ำท่วมตามปริมาณน้ำฝน (Flood Simulation)
- **Phase 3: Reactive GFM & Smart Drainage (เดือนที่ 7-9)**
  - นำโมเดล Geospatial Foundation Model (เช่น Prithvi/TerraMind) มาเชื่อมต่อเพื่อตรวจจับพื้นที่น้ำท่วมจากภาพเรดาร์ SAR อัตโนมัติ
  - พัฒนาอัลกอริทึมแนะนำการระบายน้ำและเส้นทางอพยพฉุกเฉิน
- **Phase 4: IoT Integration & Production Deployment (เดือนที่ 10-12)**
  - เชื่อมต่อข้อมูลสถานีวัดน้ำ/กล้อง CCTV ภาคสนามแบบเรียลไทม์
  - ทดสอบระบบในพื้นที่นำร่อง (Sandbox) และเปิดใช้งานจริง

---

## 8. ข้อกำหนดด้านส่วนติดต่อผู้ใช้และแดชบอร์ด (UI/UX & Dashboard Specifications)

เพื่อให้ระบบ **AIGIS** ตอบโจทย์ผู้ใช้งานตามบทบาทหน้าที่ (Role-Based Views) และสอดคล้องกับช่วงเวลาของการจัดการภัยพิบัติ ระบบจึงแบ่งส่วนการแสดงผลออกเป็น **5 Dashboards หลัก** ดังนี้:

### 8.1 Executive Command & AI Copilot Dashboard (ศูนย์บัญชาการหลักและผู้ช่วย AI)
- **กลุ่มผู้ใช้งานหลัก:** ผู้บริหารระดับสูง, ผู้อำนวยการศูนย์สั่งการ
- **วัตถุประสงค์:** ติดตามภาพรวมสถานการณ์ระดับมหภาค และสั่งการ/สอบถามข้อมูลเชิงลึกด้วยภาษาธรรมชาติ
- **องค์ประกอบหลัก (Key Components & Widgets):**
  - **AI Chat & Command Panel:** หน้าต่างสนทนา (LLM + MCP Interface) สำหรับพิมพ์หรือสั่งงานด้วยเสียง เช่น *"สรุปพื้นที่เสี่ยงวิกฤตในอีก 12 ชั่วโมง"* โดยระบบจะซูมแผนที่และเปิดเลเยอร์ที่เกี่ยวข้องให้อัตโนมัติ
  - **Real-time Situation Map (2D/3D):** แผนที่หลักแสดงภาพรวมระดับประเทศ/ลุ่มน้ำ/จังหวัด ซ้อนทับด้วยเลเยอร์ขอบเขตน้ำท่วมจากดาวเทียมล่าสุด
  - **Key Risk Indicators (KRI Cards):** การ์ดแสดงตัวเลขสรุปสำคัญแบบเรียลไทม์ ได้แก่ จำนวนพื้นที่ได้รับผลกระทบ, ประมาณการครัวเรือนในพื้นที่เสี่ยง, ปริมาณน้ำสะสมเฉลี่ย และระดับความรุนแรงของสถานการณ์ (ปกติ / เฝ้าระวัง / วิกฤต)
  - **Automated Executive Summary:** กล่องข้อความสรุปสถานการณ์ประจำวันพร้อมคำแนะนำเชิงนโยบายที่สร้างโดย AI อัตโนมัติ

### 8.2 Proactive Forecasting & Simulation Dashboard (แดชบอร์ดคาดการณ์และจำลองสถานการณ์ก่อนเกิดภัย)
- **กลุ่มผู้ใช้งานหลัก:** นักอุทกวิทยา, นักวิเคราะห์ข้อมูลเชิงพื้นที่, ทีมวางแผนป้องกันภัย
- **วัตถุประสงค์:** เฝ้าระวังและจำลองสถานการณ์ล่วงหน้าก่อนพายุเข้าหรือก่อนฤดูน้ำหลาก (Proactive Preparedness)
- **องค์ประกอบหลัก (Key Components & Widgets):**
  - **Weather & Soil Moisture Monitor:** กราฟและแผนที่แสดงพยากรณ์ปริมาณน้ำฝนล่วงหน้า (7-14 วัน) จาก Open-Meteo คู่กับดัชนีความชื้นในดิน (Soil Moisture) จาก Copernicus เพื่อประเมินศักยภาพการรับน้ำของดิน
  - **What-If Flood Simulator:** แถบควบคุม (Interactive Sliders) สำหรับทดลองปรับตัวแปรปริมาณน้ำฝน (เช่น 50 มม., 100 มม., 200 มม.) หรืออัตราการระบายน้ำจากเขื่อน เพื่อจำลองทิศทางการไหลของน้ำ (Flow Direction) และพื้นที่น้ำท่วมขังบนข้อมูล DEM
  - **Pre-Flood Action Checklist:** รายการพิกัดและแผนงานที่ AI แนะนำให้ดำเนินการล่วงหน้า เช่น จุดที่ต้องขุดลอกคูคลอง หรือเป้าหมายปริมาณน้ำที่ต้องพร่องออกจากแก้มลิง/อ่างเก็บน้ำ

### 8.3 Smart Drainage & Hydraulic Operations Dashboard (แดชบอร์ดควบคุมและบริหารจัดการการระบายน้ำ)
- **กลุ่มผู้ใช้งานหลัก:** วิศวกรชลประทาน, เจ้าหน้าที่ควบคุมประตูระบายน้ำและสถานีสูบน้ำ (กทม./ท้องถิ่น)
- **วัตถุประสงค์:** บริหารจัดการการระบายน้ำในโครงข่ายลำน้ำและคลองแบบเรียลไทม์
- **องค์ประกอบหลัก (Key Components & Widgets):**
  - **Water Network Schematic & Map:** แผนผังโครงข่ายลำน้ำ คลองระบายน้ำ สถานีวัดระดับน้ำ (Telemetering) ประตูระบายน้ำ และสถานีสูบน้ำ พร้อมสถานะการทำงานแบบเรียลไทม์ (เปิด/ปิด/กำลังสูบ/ขัดข้อง)
  - **Cross-Section Water Level Profile:** กราฟแสดงระดับน้ำในคลองเทียบกับระดับตลิ่งและเกณฑ์วิกฤต (ปกติ-สีเขียว, เฝ้าระวัง-สีเหลือง, วิกฤต-สีแดง)
  - **AI Drainage Optimizer:** หน้าต่างแสดงคำแนะนำจาก AI ในการปรับระยะยกบานประตูระบายน้ำ หรือการเดินเครื่องสูบน้ำแต่ละสถานี เพื่อเร่งระบายน้ำออกจากพื้นที่เศรษฐกิจโดยไม่ทำให้พื้นที่ท้ายน้ำล้นตลิ่ง
  - **CCTV Live Feeds & AI Vision:** หน้าจอแสดงภาพสดจากกล้อง CCTV บริเวณประตูระบายน้ำและสะพานสำคัญ พร้อมระบบ AI ตรวจจับปริมาณสิ่งกีดขวางทางน้ำ (เช่น ผักตบชวาหรือขยะสะสม)

### 8.4 Emergency Response & Logistics Dashboard (แดชบอร์ดกู้ภัยและบริหารจัดการเหตุฉุกเฉิน)
- **กลุ่มผู้ใช้งานหลัก:** ทีมกู้ภัย, กรมป้องกันและบรรเทาสาธารณภัย (ปภ.), ทหาร, หน่วยงานท้องถิ่น
- **วัตถุประสงค์:** สนับสนุนการเผชิญเหตุ การอพยพ และการส่งกำลังบำรุงขณะเกิดอุทกภัย (Reactive Response)
- **องค์ประกอบหลัก (Key Components & Widgets):**
  - **SAR Satellite Flood Extent Viewer:** แผนที่แสดงขอบเขตน้ำท่วมจริงจากภาพถ่ายดาวเทียมเรดาร์ (Sentinel-1 / APSCO) พร้อมโหมดเปรียบเทียบภาพก่อน-หลัง (Swipe/Split View) เพื่อดูแนวโน้มการขยายตัวหรือลดลงของมวลน้ำ
  - **Critical Infrastructure Impact Panel:** รายชื่อและพิกัดสถานที่สำคัญที่ถูกน้ำท่วมหรือถูกตัดขาด (โรงพยาบาล, โรงเรียน, สถานีไฟฟ้า, ถนนสายหลัก) จากการวิเคราะห์ซ้อนทับกับข้อมูล OpenStreetMap และ UN OCHA HDX
  - **Smart Evacuation & Relief Routing:** ระบบคำนวณเส้นทางอพยพและเส้นทางขนส่งถุงยังชีพผ่าน Google Maps API ที่หลีกเลี่ยงถนนที่ถูกน้ำท่วมขังโดยอัตโนมัติ พร้อมแสดงตำแหน่งศูนย์พักพิงชั่วคราวและจุดจอดเฮลิคอปเตอร์
  - **Crowdsourced Incident Tracker:** รายการแจ้งเหตุขอความช่วยเหลือจากประชาชนในพื้นที่แบบเรียลไทม์ (จำแนกตามความเร่งด่วน เช่น ผู้ป่วยติดเตียง, คนติดค้าง, ถนนขาด, ไฟดับ)

### 8.5 Public Warning & Citizen Portal Dashboard (แดชบอร์ดแจ้งเตือนภัยสำหรับประชาชน)
- **กลุ่มผู้ใช้งานหลัก:** ประชาชนทั่วไป (รองรับการใช้งานผ่าน Web Browser และ Mobile Responsive เต็มรูปแบบ)
- **วัตถุประสงค์:** แจ้งเตือนภัยด้วยข้อมูลที่เข้าใจง่าย ลดความตื่นตระหนก และเปิดรับข้อมูลสถานการณ์จริงจากภาคประชาชน
- **องค์ประกอบหลัก (Key Components & Widgets):**
  - **Check My Area (เช็กจุดเสี่ยงรอบตัว):** แผนที่ใช้งานง่ายที่ให้ประชาชนปักหมุดตำแหน่งบ้านหรือใช้ GPS ปัจจุบัน เพื่อดูระดับความเสี่ยงน้ำท่วม คาดการณ์เวลาน้ำมาถึง และระดับความลึกของน้ำที่อาจเกิดขึ้น
  - **Safe Route & Shelter Finder:** แผนที่นำทางไปยังศูนย์พักพิงชั่วคราว จุดจอดรถหนีน้ำ และแสดงสถานะถนนที่สัญจรผ่านได้/ไม่ได้แบบเรียลไทม์
  - **Citizen Report Form (Ground Truth Crowdsourcing):** ฟีเจอร์สำหรับให้ประชาชนถ่ายภาพและปักหมุดรายงานสถานการณ์น้ำท่วมจริงหน้างาน เพื่อส่งข้อมูลกลับเข้ามาช่วยตรวจสอบความถูกต้องและปรับปรุงความแม่นยำของโมเดล AI

### 8.6 ตารางสรุปความเชื่อมโยงของแต่ละ Dashboard (Dashboard Mapping Matrix)

| ชื่อ Dashboard | ผู้ใช้งานหลัก | แหล่งข้อมูลและโมเดล AI ที่ใช้งานหลัก |
| :--- | :--- | :--- |
| **1. Executive & AI Copilot** | ผู้บริหาร, ผอ.ศูนย์สั่งการ | LLM + MCP Servers ทุกตัว, ข้อมูลสรุปจากทุกโมเดล |
| **2. Proactive Forecasting** | นักวางแผน, นักอุทกวิทยา | USGS DEM, Copernicus (ERA5/Soil Moisture), Open-Meteo, Hydrological Model |
| **3. Smart Drainage Ops** | กรมชลประทาน, กทม./ท้องถิ่น | เซ็นเซอร์วัดน้ำ IoT, CCTV, Hydrological & Optimization Models |
| **4. Emergency & Logistics** | ทีมกู้ภัย, ปภ., ทหาร | Sentinel-1 (SAR), APSCO, Prithvi/TerraMind GFM, Google Maps, OSM, HDX |
| **5. Public Warning Portal** | ประชาชนทั่วไป | Google Maps, ระบบแจ้งเตือนภัย (Alerts), Crowdsourcing Database |