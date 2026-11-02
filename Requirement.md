AIGIS: AI-driven Integrated Geospatial & Inundation System
Software Requirement Specification (SRS)

1. ภาพรวมของระบบ (Project Overview)
AIGIS (AI-driven Integrated Geospatial & Inundation System) คือแพลตฟอร์มอัจฉริยะด้านภูมิสารสนเทศและการบริหารจัดการอุทกภัยแบบครบวงจร ระบบทำการบูรณาการข้อมูลจากดาวเทียมสำรวจโลก ข้อมูลสภาพอากาศ แผนที่ฐาน และข้อมูลตรวจวัดภาคสนาม เข้ากับขุมพลังของ Large Language Models (LLM) ที่เชื่อมต่อผ่าน Model Context Protocol (MCP) และ Geospatial Foundation Models (GFMs) เพื่อช่วยในการคาดการณ์จำลองสถานการณ์น้ำท่วมล่วงหน้า (Proactive) และการวางแผนระบายน้ำรวมถึงการกู้ภัยขณะเกิดเหตุ (Reactive) ผ่านการสั่งงานด้วยภาษาธรรมชาติ (Natural Language Interface)
1.1 วัตถุประสงค์ (Objectives)
เพื่อรวมศูนย์ข้อมูลดาวเทียมและข้อมูลภูมิสารสนเทศจากแหล่งข้อมูลเปิด (Open Data) และข้อมูลท้องถิ่นเข้าด้วยกันแบบอัตโนมัติ
เพื่อให้นักวิเคราะห์ เจ้าหน้าที่รัฐ และผู้บริหาร สามารถสั่งการและสอบถามข้อมูลเชิงพื้นที่ที่ซับซ้อนผ่านการสนทนาภาษาธรรมชาติ (Chatbot/Agent)
เพื่อจำลองและคาดการณ์พื้นที่เสี่ยงอุทกภัยล่วงหน้า พร้อมเสนอแนะแนวทางการเตรียมพร่องน้ำและการขุดลอกคูคลอง
เพื่อติดตามขอบเขตน้ำท่วมแบบเรียลไทม์ด้วยภาพถ่ายดาวเทียมเรดาร์ (SAR) และวางแผนการระบายน้ำรวมถึงเส้นทางอพยพฉุกเฉิน

2. สถาปัตยกรรมระบบ (System Architecture)
ระบบแบ่งการทำงานออกเป็น 5 ชั้น (Layers) หลัก ดังนี้:

2.1 Data Sources Layer (ชั้นแหล่งข้อมูล):
2.1.1 ข้อมูลดาวเทียม: Copernicus (Sentinel-1 SAR, Sentinel-2 Optical, Sentinel-3), APSCO DSSP, USGS EarthExplorer / NASA LP DAAC (Landsat, SRTM/ALOS DEM)
2.1.2 ข้อมูลสภาพอากาศและภูมิอากาศ: Copernicus ERA5 (Climate Data Store), Open-Meteo API, เรดาร์ตรวจอากาศท้องถิ่น
2.1.3 ข้อมูลแผนที่และโครงสร้างพื้นฐาน: Google Maps Platform (Base Map, Routing, Places), OpenStreetMap (OSM), UN OCHA HDX (ขอบเขตการปกครองและข้อมูลมนุษยธรรม)
2.1.4 ข้อมูลภาคสนามแบบเรียลไทม์ (IoT & Ground Truth): สถานีวัดระดับน้ำ (Telemetering), สถานีวัดน้ำฝน, กล้อง CCTV, ข้อมูลรายงานจากภาคประชาชน (Crowdsourcing)

2.2 Data Management & MCP Layer (ชั้นการจัดการข้อมูลและตัวเชื่อมต่อ):
2.2.1 MCP Servers: ตัวกลางมาตรฐานสำหรับให้ LLM เรียกใช้งานเครื่องมือและดึงข้อมูลจาก CDSE (Copernicus), CDS, OSM, Weather APIs และ Google Maps API
2.2.2 Data Ingestion & Preprocessing: ระบบดึงข้อมูล ทำความสะอาด แปลงพิกัดภูมิศาสตร์ (CRS Alignment) และจัดทำ Cloud-Optimized GeoTIFF (COG)
2.2.3 Geospatial Database: PostgreSQL + PostGIS, Vector Database และ SpatioTemporal Asset Catalog (STAC)

2.3 AI & Analytics Layer (ชั้นปัญญาประดิษฐ์และโมเดลวิเคราะห์):
2.3.1 LLM + Agentic Workflow: ประมวลผลคำสั่งภาษาธรรมชาติ วางแผนการเรียกใช้เครื่องมือผ่าน MCP และสรุปรายงาน
2.3.2 Geospatial Foundation Models (GFMs): ใช้โมเดลเช่น Prithvi (NASA/IBM), TerraMind หรือ Tessera ในการสกัดข้อมูลพื้นที่น้ำท่วม (Flood Segmentation) และการจำแนกการใช้ประโยชน์ที่ดิน (Land Cover)
2.3.3 Hydrological & Flood Modeling Engine: โมเดลคำนวณทิศทางการไหลของน้ำ (Flow Direction/Accumulation), ปริมาณน้ำหลาก (Runoff) และจำลองการระบายน้ำ

2.4 Application Layer (ชั้นแอปพลิเคชันและฟีเจอร์ใช้งาน):
2.4.1 โมดูลเตรียมการก่อนเกิดภัย (Proactive Preparedness)
2.4.2 โมดูลบริหารจัดการขณะเกิดภัย (Reactive Emergency Response)

2.5 Presentation / Output Layer (ชั้นการแสดงผล):
2.5.1 Interactive 2D/3D Web GIS Map, Conversational AI Interface, ระบบ Dashboard รายงานผล และระบบแจ้งเตือนภัย (Multi-channel Alerts)

3. ความต้องการเชิงฟังก์ชัน (Functional Requirements - FR)
FR-01: ระบบสั่งงานและวิเคราะห์ด้วยภาษาธรรมชาติ (Conversational AI & MCP Integration)
FR-01.1: ผู้ใช้สามารถพิมพ์คำสั่งด้วยภาษาธรรมชาติ (ภาษาไทยและภาษาอังกฤษ) เพื่อค้นหาข้อมูลดาวเทียม สภาพอากาศ และแผนที่ได้ (เช่น "ขอดูพื้นที่เสี่ยงน้ำท่วมในจังหวัดพระนครศรีอยุธยา หากมีฝนตกหนัก 150 มม. ใน 24 ชั่วโมงข้างหน้า")
FR-01.2: LLM ต้องสามารถเรียกใช้งาน MCP Server ต่างๆ แบบอัตโนมัติ (Tool Calling) เพื่อดึงข้อมูลจริงมาประกอบการวิเคราะห์และวาดผลลัพธ์ลงบนแผนที่

FR-02: ระบบบูรณาการข้อมูลหลายแหล่ง (Multi-Source Data Fusion)
FR-02.1: ระบบสามารถดึงภาพถ่ายดาวเทียม Sentinel-1 (SAR) และ Sentinel-2 (Optical) ล่าสุดผ่าน Copernicus MCP Server
FR-02.2: ระบบสามารถดึงข้อมูลความสูงภูมิประเทศ (DEM) และข้อมูลโครงข่ายถนน/ลำน้ำจาก OpenStreetMap และ Google Maps
FR-02.3: ระบบสามารถดึงข้อมูลพยากรณ์อากาศล่วงหน้าและค่าความชื้นในดิน (Soil Moisture) แบบอัตโนมัติ

FR-03: โมดูลการเตรียมการก่อนเกิดอุทกภัย (Proactive Flood Preparedness)
FR-03.1 การจำลองเส้นทางน้ำไหล (Flow Accumulation & Direction): วิเคราะห์ข้อมูล DEM เพื่อระบุทิศทางการไหลของน้ำและจุดแอ่งกระทะที่มีความเสี่ยงน้ำท่วมขัง
FR-03.2 การประเมินความอิ่มตัวของดินและปริมาณน้ำหลาก: คำนวณความสามารถในการซับน้ำของดินจากข้อมูลความชื้นในดินร่วมกับปริมาณฝนพยากรณ์
FR-03.3 การจำลองสถานการณ์น้ำท่วม (What-If Flood Simulation): จำลองระดับน้ำท่วมขังภายใต้ปริมาณน้ำฝนระดับต่างๆ (เช่น ฝน 50 มม., 100 มม., 200 มม.)
FR-03.4 การวางแผนพร่องน้ำและบำรุงรักษา: แนะนำจุดที่ควรขุดลอกคูคลอง กำจัดสิ่งกีดขวางทางน้ำ หรือระดับการพร่องน้ำในอ่างเก็บน้ำ/แก้มลิงล่วงหน้า

FR-04: โมดูลการบริหารจัดการขณะเกิดอุทกภัย (Reactive Flood Management)
FR-04.1 การตรวจจับพื้นที่น้ำท่วมแบบเรียลไทม์ด้วย AI: ใช้ภาพเรดาร์ SAR (ซึ่งทะลุเมฆได้ในขณะพายุเข้า) ประมวลผลผ่านโมเดล GFM เพื่อแยกแยะขอบเขตพื้นที่น้ำท่วมจริงรายวัน/รายชั่วโมง
FR-04.2 การประเมินผลกระทบต่อโครงสร้างพื้นฐาน: วิเคราะห์การซ้อนทับ (Spatial Overlay) ระหว่างพื้นที่น้ำท่วมกับข้อมูลถนน โรงพยาบาล โรงเรียน และชุมชน เพื่อระบุจุดวิกฤตที่ถูกตัดขาด
FR-04.3 ระบบแนะนำการระบายน้ำอัจฉริยะ (Smart Drainage Optimization): แนะนำลำดับการเปิด-ปิดประตูระบายน้ำ และการเดินเครื่องสูบน้ำ เพื่อระบายน้ำออกจากพื้นที่เศรษฐกิจหรือชุมชนลงสู่แม่น้ำสายหลักให้เร็วที่สุดโดยไม่กระทบพื้นที่ปลายน้ำเกินเกณฑ์
FR-04.4 การวางแผนเส้นทางฉุกเฉินและอพยพ (Emergency Routing): เชื่อมต่อ Google Maps Routing API โดยหักลบเส้นทางที่ถูกน้ำท่วมออก เพื่อแนะนำเส้นทางอพยพหรือเส้นทางลำเลียงความช่วยเหลือที่ปลอดภัยที่สุด

FR-05: ระบบการแสดงผลและแจ้งเตือน (Visualization & Alerting)
FR-05.1 Interactive GIS Dashboard: แสดงแผนที่หลายเลเยอร์ (Multi-layer Map) ที่ผู้ใช้สามารถเปิด/ปิดเลเยอร์ภาพดาวเทียม ทิศทางน้ำไหล ขอบเขตน้ำท่วม และตำแหน่งสถานีวัดน้ำได้
FR-05.2 Automated Report Generation: สร้างสรุปรายงานสถานการณ์สำหรับผู้บริหารพร้อมกราฟและแผนที่โดยอัตโนมัติ
FR-05.3 Real-time Alerting: ส่งการแจ้งเตือนผ่าน API, LINE OA, SMS หรือ Web Push เมื่อระดับน้ำหรือค่าพยากรณ์เกินเกณฑ์วิกฤต (Threshold)

4. ความต้องการด้านข้อมูลและตัวเชื่อมต่อ (Data & MCP Interfaces)
แหล่งข้อมูล (Data Source)	  ประเภทข้อมูล	                                        วิธีการเชื่อมต่อ (Integration Method)
========================  ================================================    =========================================
Copernicus (CDSE & CDS)	  Sentinel-1/2/3, ERA5 Climate, Soil Moisture	        Copernicus MCP Server / STAC API / openEO
APSCO DSSP	              ข้อมูลดาวเทียมพันธมิตรเอเชีย-แปซิฟิกเพื่อภัยพิบัติ	            REST API / Custom MCP Connector
Google Maps Platform	    Base Map, Places, Street View, Traffic & Routing	  Google Maps API / MCP Server
OpenStreetMap (OSM)	      โครงข่ายถนน อาคาร ลำน้ำ โครงสร้างพื้นฐาน	                Overpass API / OSM MCP Server
USGS / NASA LP DAAC	      DEM (SRTM, ALOS), Landsat	                          EarthExplorer API / STAC
Open-Meteo	              พยากรณ์อากาศรายชั่วโมง ปริมาณฝน ลม	                    Open-Meteo REST API / Weather MCP
UN OCHA HDX	              ขอบเขตการปกครอง (ADM), ข้อมูลประชากรและกลุ่มเปราะบาง	  HDX HAPI / CKAN API
Local IoT / ThaiWater	    ระดับน้ำในคลอง ปริมาณฝนจริง สถานะประตูระบายน้ำ	          Webhook / REST API / MQTT

5. ความต้องการที่ไม่ใช่เชิงฟังก์ชัน (Non-Functional Requirements - NFR)
NFR-01 ด้านประสิทธิภาพ (Performance):
การตอบกลับของ LLM เบื้องต้นต้องใช้เวลาไม่เกิน 3-5 วินาที
การเรนเดอร์แผนที่ความละเอียดสูง (Tile Rendering) ต้องโหลดเสร็จภายใน 2 วินาทีผ่านโครงสร้าง Cloud-Optimized GeoTIFF (COG)

NFR-02 ด้านความสามารถในการขยายตัว (Scalability):
สถาปัตยกรรมต้องรองรับการทำงานแบบ Microservices และ Containerization (Docker/Kubernetes) เพื่อขยายทรัพยากรการประมวลผล AI บนคลาวด์ได้อัตโนมัติเมื่อเกิดเหตุภัยพิบัติใหญ่

NFR-03 ด้านความเข้ากันได้และมาตรฐาน (Interoperability):
ข้อมูลภูมิสารสนเทศทั้งหมดต้องเป็นไปตามมาตรฐาน OGC (Open Geospatial Consortium) เช่น WMS, WFS, GeoJSON, COG และ STAC
การเชื่อมต่อเครื่องมือ AI ใช้มาตรฐาน Model Context Protocol (MCP) เพื่อให้สามารถสลับเปลี่ยนโมเดล LLM ได้อย่างอิสระ

NFR-04 ด้านความมั่นคงปลอดภัย (Security & Privacy):
มีการจัดการสิทธิ์การเข้าถึงข้อมูล (Role-Based Access Control - RBAC) สำหรับประชาชนทั่วไป เจ้าหน้าที่ปฏิบัติการ และผู้บริหารที่มีสิทธิ์สั่งการระบบระบายน้ำ

6. เทคโนโลยีที่แนะนำในการพัฒนา (Proposed Tech Stack)
Frontend: Next.js (React), Tailwind CSS, Mapbox GL JS / Deck.gl / Leaflet, Google Maps JS SDK
Backend & API: Python (FastAPI), Node.js
AI & Agent Orchestration: Model Context Protocol (MCP) SDK, LangChain / LlamaIndex, Claude / Gemini / OpenAI APIs
Geospatial AI & Processing: PyTorch, TorchGeo, Prithvi / TerraMind (Hugging Face), GDAL, Rasterio, GeoPandas, WhiteboxTools (สำหรับ Hydrological Modeling)
Database & Storage: PostgreSQL + PostGIS, pgvector, AWS S3 / MinIO (สำหรับเก็บไฟล์ COG/STAC)

7. แผนการพัฒนาโครงการ (Development Roadmap)
Phase 1: Foundation & MCP Integration (เดือนที่ 1-3)
          พัฒนาและเชื่อมต่อ MCP Servers สำหรับ Copernicus, Open-Meteo, OSM และ Google Maps
          สร้างหน้าจอ Chat Interface คู่กับแผนที่พื้นฐาน (Map UI)

Phase 2: Proactive Hydrological Modeling (เดือนที่ 4-6)
          พัฒนาโมดูลวิเคราะห์ DEM ทิศทางน้ำไหล และการประเมินความชื้นในดิน
          สร้างฟีเจอร์จำลองสถานการณ์น้ำท่วมตามปริมาณน้ำฝน (Flood Simulation)

Phase 3: Reactive GFM & Smart Drainage (เดือนที่ 7-9)
          นำโมเดล Geospatial Foundation Model (เช่น Prithvi/TerraMind) มาเชื่อมต่อเพื่อตรวจจับพื้นที่น้ำท่วมจากภาพเรดาร์ SAR อัตโนมัติ
          พัฒนาอัลกอริทึมแนะนำการระบายน้ำและเส้นทางอพยพฉุกเฉิน
          
Phase 4: IoT Integration & Production Deployment (เดือนที่ 10-12)
          เชื่อมต่อข้อมูลสถานีวัดน้ำ/กล้อง CCTV ภาคสนามแบบเรียลไทม์
          ทดสอบระบบในพื้นที่นำร่อง (Sandbox) และเปิดใช้งานจริง
