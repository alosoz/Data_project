# 🏨 HappyBooking Data Engineering Pipeline

**Booking.com Esinli, Modern Data Stack ile Uçtan Uca Data Engineering Projesi**

---

## 📋 Proje Özeti

HappyBooking, farklı kaynaklardan gelen otel rezervasyon verilerini **batch + stream** olarak toplamak, işlemek ve analizler üretmek için bir **modern data pipeline** kuruyor.

Bu proje, gerçek dünyaya yakın bir senaryoda Microsoft Fabric, Docker, DBT, Great Expectations ve Power BI kullanarak bir data engineering ekosistemi inşa eder.

---

## 🎯 Hedefler

### Teknik Hedefler
- ✅ **Bronze → Silver → Gold** (Delta Lake) mimarisi uygulamak
- ✅ **Stream + Batch** veri entegrasyonunu sağlamak
- ✅ **Docker** tabanlı stream simulator geliştirmek
- ✅ **Great Expectations** ile veri kalitesini doğrulamak
- ✅ **DBT** modelleri ile Gold layer KPI'ları hesaplamak
- ✅ **Power BI** dashboard'ları ile analiz sunmak
- ✅ **(Opsiyonel)** Airflow + GitHub Actions entegrasyonu

### Business Hedefleri
- Hızlı veri işleme (batch: günlük, stream: real-time)
- Yüksek veri kalitesi (%95+ pass rate)
- Yönetim KPI'ları raporlamak (revenue, cancellation, occupancy)
- Scalable ve maintainable architecture

---

## 🏗️ Mimari Özet

```
┌─────────────────────────────────────────────────────┐
│                   DATA SOURCES                      │
├─────────────────────────────────────────────────────┤
│  • CSV/JSON (Kaggle, TripAdvisor)                   │
│  • Docker Stream Simulator (Python)                 │
│  • API Endpoints (opsiyonel)                        │
└────────────┬──────────────────────────┬─────────────┘
             │                          │
             v                          v
    ┌────────────────────┐   ┌──────────────────────┐
    │ Batch Processing   │   │ Streaming (Event)    │
    │ (Fabric Import)    │   │ (Eventstream → KQL)  │
    └────────┬───────────┘   └──────────┬───────────┘
             │                          │
             └──────────────┬───────────┘
                            v
            ┌───────────────────────────────┐
            │  BRONZE LAYER (Raw/Lakehouse) │
            │  - booking_raw                │
            │  - booking_stream             │
            └───────────┬─────────────────┘
                        │
                        v
            ┌───────────────────────────────┐
            │  SILVER LAYER (Cleaned)       │
            │  - booking_cleaned            │
            │  - city_enriched              │
            │  (PySpark + Great Expectations)
            └───────────┬─────────────────┘
                        │
                        v
            ┌───────────────────────────────┐
            │  GOLD LAYER (Analytics Ready) │
            │  - fact_booking               │
            │  - dim_city, dim_hotel, etc   │
            │  - kpi_revenue_daily          │
            │  - kpi_cancellation_rate      │
            │  (DBT Models)                 │
            └───────────┬─────────────────┘
                        │
         ┌──────────────┴──────────────┐
         v                             v
    ┌──────────────┐          ┌──────────────┐
    │  Power BI    │          │  Warehouse/  │
    │  Dashboard   │          │  Analytics   │
    └──────────────┘          └──────────────┘
```

---

## 🛠️ Teknoloji Stack

### Core Tools
| Tool | Kullanım | Versiyon |
|------|----------|---------|
| **Microsoft Fabric** | Lakehouse, Eventstream, Notebook, Warehouse | Latest |
| **Apache PySpark** | Transformation | 3.3+ |
| **DBT** | Gold Layer Modeling | 1.5+ |
| **Great Expectations** | Data Quality | 0.17+ |
| **Docker** | Stream Simulator | 20.10+ |
| **Python** | Script, Notebook | 3.9+ |

### Opsiyonel Tools
| Tool | Kullanım |
|------|----------|
| **Apache Airflow** | Orkestrasyon alternatifi |
| **GitHub Actions** | CI/CD Pipeline |
| **Power BI** | Visualization |

---

## 📁 Klasör Yapısı

```
happybooking-data-pipeline/
│
├── 📂 infrastructure/              # IaC, deployment configs
│   ├── terraform/                  # Opsiyonel Terraform scripts
│   └── manifests/                  # Kubernetes, Docker Compose
│
├── 📂 docker/                      # Stream simulator
│   ├── Dockerfile
│   ├── stream_producer.py          # Booking event generator
│   ├── requirements.txt
│   └── config/
│
├── 📂 notebooks/                   # Fabric Notebooks
│   ├── 01_bronze_ingest_batch.py
│   ├── 02_stream_to_bronze.py
│   ├── 03_silver_transformations.py
│   ├── 04_gold_dbt_run.py          # DBT trigger notebook
│   ├── 05_quality_tests_ge.py
│   └── schemas/
│       ├── bronze_booking_schema.py
│       └── silver_booking_schema.py
│
├── 📂 dbt_project/                 # DBT Models & Tests
│   ├── dbt_project.yml
│   ├── profiles.yml
│   ├── models/
│   │   ├── staging/
│   │   ├── mart/
│   │   │   ├── fact_booking.sql
│   │   │   ├── dim_city.sql
│   │   │   ├── dim_hotel.sql
│   │   │   └── dim_date.sql
│   │   └── kpi/
│   │       ├── kpi_revenue_daily.sql
│   │       ├── kpi_cancellation_rate.sql
│   │       └── kpi_occupancy.sql
│   ├── tests/
│   │   ├── assert_positive_revenue.sql
│   │   └── assert_unique_booking_id.sql
│   └── macros/
│
├── 📂 tests/                       # Test Suites
│   ├── great_expectations/
│   │   ├── great_expectations.yml
│   │   ├── expectations/
│   │   │   └── silver_booking.json
│   │   └── checkpoints/
│   ├── pytest/
│   │   └── test_transformations.py
│   └── dbt_tests/                  # DBT Built-in tests
│
├── 📂 data/                        # Sample Data
│   ├── raw/
│   │   ├── hotel_booking_demand.csv
│   │   ├── tripadvisor_reviews.csv
│   │   └── city_reference.csv
│   └── processed/
│
├── 📂 docs/                        # Documentation
│   ├── README.md                   # This file
│   ├── ARCHITECTURE.md             # Detailed architecture
│   ├── SETUP.md                    # Installation guide
│   ├── TROUBLESHOOTING.md          # Common issues & solutions
│   ├── runbooks/
│   │   ├── manual_trigger_pipeline.md
│   │   ├── debug_pipeline.md
│   │   └── data_quality_issues.md
│   └── diagrams/
│       ├── architecture.drawio
│       └── data_flow.drawio
│
├── 📂 pipelines/                   # Orchestration
│   ├── fabric_pipeline.json        # Fabric Pipeline export
│   ├── airflow/
│   │   ├── dags/
│   │   │   └── booking_dag.py
│   │   └── docker-compose.yml
│   └── scheduling/
│       └── schedule_config.yaml
│
├── 📂 .github/                     # GitHub Configuration
│   ├── workflows/
│   │   ├── dbt_test.yml           # DBT test on PR
│   │   ├── data_quality_check.yml # Great Expectations on PR
│   │   └── deploy_notebook.yml    # Deploy to Fabric
│   └── ISSUE_TEMPLATE/
│       └── bug_report.md
│
├── .gitignore                      # Git ignore patterns
├── requirements.txt                # Python dependencies
├── pyproject.toml                  # Project metadata
└── LICENSE                         # MIT License
```

---

## 🚀 Hızlı Başlangıç

### Ön Koşullar
- **Python 3.9+** yüklü
- **Git** kurulu
- **Docker** ve **Docker Compose** (stream simulator için)
- **Microsoft Fabric** hesabı (Power BI Premium gerekli)
- **DBT Cloud** hesabı (opsiyonel, veya local DBT)

### Adım 1: Repository Klonla
```bash
git clone https://github.com/[username]/happybooking-data-pipeline.git
cd happybooking-data-pipeline
```

### Adım 2: Python Environment Kurulumu
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
# veya
venv\Scripts\activate      # Windows

pip install -r requirements.txt
```

### Adım 3: Fabric Workspace Oluştur
1. [Power BI](https://app.powerbi.com) aç
2. **Workspace** oluştur: `HappyBooking_DataLake`
3. **Lakehouse** oluştur: `booking_lakehouse`
4. Workspace ID'sini not et (credentials dosyası için)

### Adım 4: Docker Stream Simulator Başlat
```bash
cd docker
docker build -t happybooking-simulator .
docker run --rm \
  -e EVENTSTREAM_CONNECTION_STRING="your_connection_string" \
  happybooking-simulator
```

### Adım 5: Fabric Notebooks'ü Import Et
1. Fabric workspace'e git
2. **Notebook** oluştur
3. `notebooks/01_bronze_ingest_batch.py` içeriğini kopyala
4. Tekrarla: `02_stream_to_bronze.py`, `03_silver_transformations.py`, `05_quality_tests_ge.py`

### Adım 6: DBT Kurulumu
```bash
cd dbt_project
dbt debug  # Bağlantı kontrolü
dbt run    # Modelleri çalıştır
dbt test   # Testleri çalıştır
```

### Adım 7: Pipeline'ı Çalıştır
Fabric'te **Pipeline** oluştur ve adımları sırasıyla ekle:
1. Bronze Ingestion (Notebook 01)
2. Stream Ingestion (Notebook 02)
3. Silver Transformation (Notebook 03)
4. DBT Models (Notebook 04)
5. Data Quality (Notebook 05)

### Adım 8: Power BI Dashboard
1. Power BI Desktop aç
2. Fabric Warehouse'a bağlan
3. `notebooks/dashboards/booking_dashboard.pbix` aç
4. Service'e publish et

---

## 📊 Veri Setleri

| Dataset | Kaynak | Format | Boyut | Açıklama |
|---------|--------|--------|-------|----------|
| Hotel Booking Demand | [Kaggle](https://www.kaggle.com/jessemostipak/hotel-booking-demand) | CSV | ~45 MB | Tarihsel rezervasyon verileri |
| TripAdvisor Reviews | [Kaggle](https://www.kaggle.com/datasets/andrewmvd/hotel-reviews) | CSV | ~50 MB | Otel reviews ve ratings |
| City Reference | Generated | CSV | ~1 MB | Şehir, ülke, bölge enrichment |
| Weather Data | OpenWeatherMap (mock) | JSON | ~2 MB | Tarihsel hava durumu |

---

## 🔄 Pipeline Akışı

### Batch Pipeline (Günlük, 02:00 UTC)
```
1. Raw CSV → Bronze (Lakehouse import)
2. Bronze → Silver (PySpark dönüşüm)
3. Great Expectations validation
4. Silver → Gold (DBT models)
5. Power BI refresh trigger
```

### Streaming Pipeline (Her 10 dakika)
```
1. Docker Simulator → Event Stream
2. Eventstream → KQL DB (real-time analytics)
3. Eventstream → Bronze (Lakehouse delta table)
4. Bronze → Silver (incremental)
5. Anomaly detection (opsiyonel)
```

---

## 📈 Key Metrics (KPI'lar)

Aşağıdaki metrikler Gold layer'da hesaplanır:

| KPI | Tanım | Frequency |
|-----|-------|-----------|
| **Total Revenue** | Toplam gelir (EUR) | Daily |
| **Booking Count** | Toplam rezervasyon sayısı | Daily |
| **Cancellation Rate** | İptal oranı (%) | Daily |
| **Avg Length of Stay** | Ortalama konaklama süresi | Daily |
| **Occupancy Rate** | Doluluk oranı (%) | Daily |
| **Revenue per Booking** | Ortalama rezervasyon geliri | Daily |
| **Top Cities** | En fazla rezervasyon yapılan şehirler | Weekly |
| **Cancellation Trend** | İptal eğilimi | Weekly |

---

## ✅ Başarı Kriterleri

- [x] Klasör yapısı oluşturuldu
- [ ] Fabric Workspace + Lakehouse kurulu
- [ ] Docker stream simulator çalışır
- [ ] Bronze layer veri toplar
- [ ] Silver layer temizlik yapar
- [ ] DBT Gold modelleri hesaplar
- [ ] Great Expectations testleri %100 geçer
- [ ] Power BI dashboard'ları KPI'ları gösterir
- [ ] Pipeline'lar otomatik çalışır
- [ ] Tüm kod GitHub'da versiyon kontrolü altında

---

## 🐛 Troubleshooting

Sorun mu yaşıyorsun? Bakınız: [TROUBLESHOOTING.md](./docs/TROUBLESHOOTING.md)

Sık karşılaşılan sorunlar:
- **Eventstream bağlantı hatası** → Connection string kontrol et
- **DBT validation hatası** → `dbt debug` ile bağlantıyı test et
- **Great Expectations fail** → Test expectations'ı gözden geçir

---

## 📚 Dokümantasyon

- **[ARCHITECTURE.md](./docs/ARCHITECTURE.md)** – Detaylı mimari açıklama
- **[SETUP.md](./docs/SETUP.md)** – Kurulum adımları
- **[RUNBOOKS](./docs/runbooks/)** – Operasyonel kılavuzlar

---

## 🤝 Katkıda Bulunmak

Pull request'ler hoş geldiniz! Lütfen:

1. Fork yap
2. Feature branch oluştur (`git checkout -b feature/amazing-feature`)
3. Commit et (`git commit -m 'Add amazing feature'`)
4. Push et (`git push origin feature/amazing-feature`)
5. Pull Request aç

### Code Standards
- Python: **PEP 8** uyumlu
- SQL: **dbt** best practices
- Git: Conventional Commits

---

## 📞 İletişim & Destek

- **Issues** → GitHub Issues sekmesi
- **Email** → happybooking-team@example.com
- **Slack** → #data-engineering (opsiyonel)

---

## 📄 Lisans

Bu proje **MIT License** altında lisanslanmıştır. Detaylar: [LICENSE](./LICENSE)

---

## 🎉 Çıktılar

Proje tamamlandığında elde edeceğin şeyler:

✅ **Çalışan data pipeline** (batch + stream)  
✅ **Docker stream simulator**  
✅ **Bronze, Silver, Gold katman tabloları**  
✅ **DBT modelleri ve dokumentasyonu**  
✅ **Great Expectations raporları**  
✅ **Power BI dashboard'ları**  
✅ **CI/CD pipeline'ları** (opsiyonel)  
✅ **Eksiksiz dokümantasyon**  

---

## 📈 Timeline

| Faze | Tahmini Süre |
|------|--------------|
| Setup & Infrastructure | 3 gün |
| Docker Stream Simulator | 4 gün |
| Bronze Layer | 5 gün |
| Silver Layer | 6 gün |
| Data Quality (Great Expectations) | 5 gün |
| Gold Layer (DBT) | 7 gün |
| Orchestration (Fabric Pipeline) | 5 gün |
| Power BI Dashboard | 6 gün |
| Opsiyonel (Airflow, CI/CD) | 10 gün |
| Documentation & Polish | 4 gün |
| **TOPLAM** | **~55 gün** |

---

**Son güncelleme:** 15 Kasım 2025  
**Proje Durumu:** 🔨 Kurulum aşamasında  
**Kontributor:** Data Engineering Team
