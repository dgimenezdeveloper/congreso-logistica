# 🎟️ Congreso UNaB — Logistics & Transport Conference Platform

[![Frontend](https://img.shields.io/badge/Frontend-React%2018%20%7C%20Vite%207-61DAFB?logo=react&logoColor=white)](#)
[![Backend](https://img.shields.io/badge/Backend-Django%205.2%20%7C%20DRF%203.16-092E20?logo=django&logoColor=white)](#)
[![Styling](https://img.shields.io/badge/Styling-Tailwind%20CSS%203%20%7C%20Radix%20UI-38B2AC?logo=tailwindcss&logoColor=white)](#)
[![QR Engine](https://img.shields.io/badge/QR%20Engine-Pillow%20%7C%20react--zxing-blue)](#)
[![Certificates](https://img.shields.io/badge/PDF%20Engine-xhtml2pdf%20%7C%20ReportLab-red)](#)
[![Deployment](https://img.shields.io/badge/Deploy-Netlify%20%7C%20Vercel%20%7C%20PythonAnywhere-00C7B7?logo=netlify&logoColor=white)](#)

> Full-stack event management, attendee registration, and digital accreditation platform engineered for the **National Logistics and Transport Congress 2025** at the National University of Guillermo Brown (UNaB). Features high-speed venue check-in via dual QR code gates, real-time DNI attendance confirmation, automated A4 landscape PDF certificate compilation, and transactional SMTP email delivery.
>
> 🌐 **Quick Navigation / Navegación Rápida:** [English Documentation](#-english-documentation) | [Documentación en Español](#-documentación-en-español)

---

## 🌐 English Documentation

### 1. Executive Summary & Event Scope
Organizing large-scale academic and corporate conferences often leads to severe registration bottlenecks at entrance gates, paper credential waste, and delayed certificate issuance. 

**Congreso UNaB** provides an integrated digital ecosystem developed by **Folkode Group** to manage the full lifecycle of the **Congreso de Logística y Transporte 2025** (*"Moviendo el Futuro"*), hosted at the UNaB Campus (Buenos Aires, Argentina):
- **Event Scale:** Built to manage **500+ attendees**, **30+ corporate sponsors and logistics enterprises**, and **25+ keynote speakers**.
- **Interactive Multi-Track Agenda:** Dynamic schedule matrix spanning 10 physical lecture halls (*Aula Magna* and *Aulas 1 to 10*) across 8 domain tracks (Logistics, Transport, Supply Chain, Technology, Sustainability, Innovation, Management, and Networking).
- **Zero-Friction Accreditation:** Eliminates entrance queues using high-contrast dual static QR stations allowing simultaneous check-ins on mobile devices.
- **Automated Digital Certification:** Instantly compiles vector-rendered PDF attendance certificates with unique verification timestamps and dispatches them via transactional email upon check-in.

---

### 2. System Architecture & Component Breakdown

The platform adopts a decoupled architecture isolating the interactive React single-page application from the Django REST Framework backend service:

```
congreso-logistica/
├── backend/                  # Django 5.2 + Django REST Framework 3.16
│   ├── api/
│   │   ├── models.py         # Attendee, Company, Program, Certificate, Speaker entities
│   │   ├── serializers.py    # DRF validation & relational serialization pipelines
│   │   ├── views.py          # ViewSets for individual/group intake & DNI verification
│   │   ├── qr_views.py       # Pillow PIL QR image compositor with embedded UNaB logo
│   │   ├── email.py          # Multi-alternative HTML email & xhtml2pdf compiler
│   │   └── templates/        # A4 Landscape certificate and responsive email markup
│   ├── core/                 # Django settings, CORS headers, ASGI/WSGI configs
│   └── test_email.py         # Diagnostic suite for SMTP credentials and PDF buffers
├── client/                   # React 18 + Vite 7 SPA (Port 8080 / 5173)
│   ├── components/
│   │   ├── ui/               # Radix UI + Modern Glassmorphism Form System
│   │   ├── LogoMarquee.tsx   # Continuous logo carousel with double-buffer tracks
│   │   └── TruckCarousel.tsx # Framer Motion animated SVG logistics transport fleet
│   ├── pages/                # Multi-step intake, Agenda table, QR Check-in, Speaker grid
│   └── lib/api.ts            # Typed HTTP client communicating with backend endpoints
└── netlify.toml              # Production SPA routing and proxy configurations
```

---

### 3. High-Throughput Dual QR Accreditation Architecture

To prevent physical bottlenecks at venue access points, the system replaced individual attendee QR passes with **dual physical QR stations** deployed across the campus:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       EVENT ENTRANCE ACCESS GATES                           │
├──────────────────────────────────────┬──────────────────────────────────────┤
│ 🔵 BLUE STATION: Check-In & Certs    │ 🟢 GREEN STATION: In-Situ Express    │
│ (For pre-registered attendees)       │ (For unregistered walk-in visitors)  │
├──────────────────────────────────────┼──────────────────────────────────────┤
│ 1. Attendee scans entrance poster.   │ 1. Walk-in attendee scans poster.    │
│ 2. Browser opens `/verificar-dni`.   │ 2. Browser opens `/registro-rapido`. │
│ 3. Enters 8-digit National ID (DNI). │ 3. Submits minimal intake form.      │
│ 4. Backend validates `Asistente`:    │ 4. Backend atomically creates        │
│    - Flags `asistencia_confirmada`   │    `Asistente` + `Inscripcion` and   │
│    - Sets `fecha_confirmacion`       │    confirms attendance in real time. │
│ 5. Triggers PDF compilation and      │ 5. Generates certificate and fires   │
│    sends certificate to inbox.       │    transactional email immediately.  │
└──────────────────────────────────────┴──────────────────────────────────────┘
```

#### QR Image Compositing Engine (`qr_views.py`)
Generates high-resolution PNG QR images with error-correction level `H` ($30\%$ damage tolerance). It programmatically embeds the official UNaB and Folkode logos in the center and corners with proportional padding using the Python Imaging Library (Pillow).

---

### 4. Automated Transactional Emails & PDF Certification

- **Vector PDF Compilation (`xhtml2pdf`):** Renders high-resolution, print-ready A4 landscape certificates (`asistencia.html`) including dynamic attendee names, digital authority signatures, and university crests in under $400\text{ ms}$.
- **MIME Multipart Emails:** Sends responsive HTML confirmation messages (`confirmacion.html`) containing an inline embedded logo (`cid:logo_congreso`) and an automated deep-link URL adding the event directly to **Google Calendar**.
- **Diagnostic Email Tool (`test_email.py`):** Standalone diagnostic script to verify TLS handshakes, Google App Password authorization, and template rendering without booting the web server.

---

### 5. Technical Stack Specification

| Subsystem | Technology | Version | Purpose |
| :--- | :--- | :--- | :--- |
| **Frontend Framework** | React | `^18.3.1` | Component lifecycle, hooks, and virtual DOM |
| **Build Tool** | Vite | `^7.1.2` | Fast HMR dev server and optimized production bundling |
| **Styling & UI** | Tailwind CSS + Radix UI | `^3.4` / `^1.1` | Utility-first styling with accessible headless primitives |
| **Form Handling** | React Hook Form + Zod | `^7.62` / `^3.25`| Type-safe form validation and conditional inputs |
| **Camera QR Scanning**| react-zxing / QrReader | `^2.1.0` | Browser-based camera stream for QR code decoding |
| **Animations** | Framer Motion | `^12.23.12` | Fluid scroll transitions, carousels, and modal overlays |
| **Backend Framework** | Django | `5.2.5` | Web framework, ORM, and admin back-office |
| **REST Architecture** | DRF | `3.16.1` | Serialization, JSON API ViewSets, and CORS policies |
| **PDF Generation** | xhtml2pdf & ReportLab | `latest` / `4.2`| HTML-to-PDF compilation for attendance certificates |
| **QR Generation** | qrcode[pil] | `7.4.2` | Programmatic QR generation with embedded logo overlays |
| **Email Service** | Django Core Mail (SMTP)| Built-in | TLS transactional email engine with MIME attachments |

---

### 6. Core REST API Endpoints

```
GET   /api/disertantes/        # List all keynote speakers sorted alphabetically
GET   /api/programa/           # Retrieve event schedule across rooms and categories
POST  /api/inscripcion/        # Register individual attendee (Visitor, Student, Professional)
POST  /api/inscripcion-grupal/ # Register corporate groups with multiple team members
POST  /api/registro-empresas/  # Register corporate sponsors, logos, and booth modalities
POST  /api/verificar-dni/      # Confirm attendance via DNI & trigger PDF certificate email
POST  /api/registro-rapido/    # Express walk-in registration with immediate check-in
GET   /api/generar-qrs/        # Generate high-resolution base64 QR codes for printing
```

---

### 7. Local Setup & Quick Start

#### Prerequisites
- Node.js 20+ and `pnpm` installed.
- Python 3.10+ installed.

#### Backend Setup (Django)
```bash
# 1. Clone repository & navigate to backend
git clone https://github.com/dgimenezdeveloper/congreso-logistica.git
cd congreso-logistica/backend

# 2. Create and activate virtual environment
python -m venv env
source env/bin/activate  # On Windows: env\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Configure environment variables
cp .env.example .env
# Ensure EMAIL_HOST_USER and EMAIL_HOST_PASSWORD (16-character App Password) are set.

# 5. Apply migrations and seed sample schedule/speakers
python manage.py migrate
python manage.py populate_disertantes
python manage.py populate_programas

# 6. Start backend API server
python manage.py runserver 8000
```

#### Frontend Setup (React + Vite)
```bash
# In a separate terminal, navigate to project root
cd congreso-logistica

# 1. Install dependencies
pnpm install

# 2. Start Vite development server
pnpm run dev
```

Access the frontend application at `http://localhost:8080` (or `http://localhost:5173`) and the Django Admin panel at `http://127.0.0.1:8000/admin/`.

---

## 🇪🇸 Documentación en Español

### 1. Resumen Ejecutivo y Alcance del Evento
La gestión de congresos masivos suele generar largas colas de espera en los accesos, consumo innecesario de papel en credenciales y demoras en la entrega de certificados de asistencia.

**Congreso UNaB** es una plataforma web desarrollada por **Folkode Group** para administrar el ciclo integral del **Congreso de Logística y Transporte 2025** (*"Moviendo el Futuro"*), llevado a cabo en el campus de la Universidad Nacional Guillermo Brown:
- **Capacidad y Alcance:** Diseñado para **más de 500 asistentes**, **más de 30 empresas patrocinadoras** y **más de 25 disertantes**.
- **Agenda Interactiva:** Grilla de conferencias dividida en 10 aulas (*Aula Magna* y *Aulas 1 a 10*) y 8 categorías temáticas (Logística, Transporte, Supply Chain, Tecnología, Sostenibilidad, Innovación, Gestión y Networking).
- **Acreditación sin Colas:** Puntos de acceso con códigos QR estáticos que permiten a los asistentes autogestionar su check-in en segundos desde sus propios teléfonos móviles.
- **Certificación Automática:** Generación y envío instantáneo de certificados de asistencia en PDF vectoriales de alta resolución (`xhtml2pdf`) al momento de confirmar asistencia.

---

### 2. Sistema de Acreditación Rápida con QR Dual

En lugar de emitir un código QR individual por cada asistente (que suele perderse en el correo o demorar la lectura), el congreso implementó **dos estaciones fijas con cartelería QR**:

1. **QR Azul — Confirmación de Asistencia (`/verificar-dni`):** El asistente preinscripto escanea el QR en la entrada, introduce su DNI y el sistema valida su identidad, marca su asistencia con marca temporal (`fecha_confirmacion`), genera su certificado PDF y se lo envía a su casilla de correo.
2. **QR Verde — Registro In-Situ (`/registro-rapido`):** Para personas que llegan al evento sin registro previo. Completan un formulario reducido de 3 campos, el backend crea su inscripción, confirma la asistencia de inmediato y despacha el certificado oficial.
3. **Generador Administrativo de QRs (`/generar-qrs`):** Interfaz para generar e imprimir los códigos QR en alta resolución con los logos de UNaB y Folkode incrustados mediante Pillow (PIL).

---

### 3. Motor de Certificados y Correo Transaccional

- **Compilador de Certificados PDF:** Plantilla A4 apaisada vectorizada con `xhtml2pdf` y `reportlab`, con firmas de autoridades, tipografía institucional y fecha de emisión automática.
- **Envío Transaccional SMTP:** Conexión segura con contraseñas de aplicación de 16 caracteres de Google, con soporte para adjuntar imágenes inline (`cid:logo_congreso`) y enlaces automáticos a **Google Calendar**.
- **Script de Diagnóstico (`test_email.py`):** Herramienta CLI para auditar la conexión SMTP y verificar la generación de certificados sin necesidad de iniciar el servidor.

---

### 4. Puesta en Marcha Local

```bash
# 1. Backend (Django)
cd backend
python -m venv env
source env/bin/activate  # En Windows: env\Scripts\activate
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver 8000

# 2. Frontend (React + Vite)
cd ..
pnpm install
pnpm run dev
```

---

## 👥 Equipo de Desarrollo / Project Authors

Plataforma diseñada y construida por **Folkode Group** para la **Universidad Nacional Guillermo Brown (UNaB)**:

- **Darío Giménez** — *Full-Stack Engineer, Project Lead & Systems Architect*  
  [GitHub](https://github.com/dgimenezdeveloper) • [LinkedIn](https://www.linkedin.com/in/daseg/) • [Portfolio](https://portafolio-daseg.vercel.app/)
- **Folkode Group** — *Software Factory & Development Team*  
  [Sitio Oficial](https://folkode.com.ar)

---

## 📄 License
This project is proprietary software developed for the **Universidad Nacional Guillermo Brown**. All rights reserved.
