# 🎓 Parent Info System - Multi-Institution Activity Management

**English** | [Türkçe](README.tr.md)

---

## 📖 Project Overview

Parent Info System is a comprehensive **Django-based web application** designed to help parents stay informed about their children's weekly academic and social activities across multiple institutions. The system provides a centralized platform where institution administrators can manage activities, and parents can easily search for their children's schedules using just their name and surname.

## ✨ Key Features

### 🏢 Multi-Institution Management
- System administrators can create and manage multiple institutions
- Each institution can have dedicated administrators
- Institution-specific branding and information

### 👥 Role-Based Access Control
- **System Administrators:** Full system access, institution creation, global announcements
- **Institution Administrators:** Limited to their assigned institution(s)
- **Parents:** Search-only access to view student activities

### 📅 Activity Management
- Institution administrators can add weekly academic and social activities
- Activities are organized by week for easy navigation
- Support for both academic programs and social events
- Detailed activity descriptions and scheduling

### 📢 Announcement System
- **Global Announcements:** System administrators create base announcements
- **Institution-Specific Details:** Institution administrators can add custom information (time, location, etc.)
- **Automatic Information:** System automatically appends institution address and contact details

### 🔍 Parent Search Portal
- Parents can search for their child's activities using name and surname
- Simple, intuitive interface
- No login required for parents
- Weekly activity overview

## 🏗️ Architecture & Technology Stack

### Application Layer
- **Framework:** Django 4.x (Python)
- **Database:** PostgreSQL with CloudNative PostgreSQL Operator
- **Object Storage:** Cloudflare R2 for static and media files
- **Container Runtime:** Docker

### Infrastructure Layer
- **Orchestration:** On-Premise Kubernetes Cluster
- **GitOps:** ArgoCD for continuous deployment
- **Storage:** Longhorn for persistent volume management
- **Database Operator:** CloudNative PostgreSQL (CNPG)
- **Network:** Cloudflare Tunnel for secure external access

### CI/CD Pipeline
```
GitHub Push → GitHub Actions → Build Container → Update k8s-infra → ArgoCD Sync → Deploy
```

1. **Code Push:** Developer pushes code to GitHub
2. **Automated Build:** GitHub Actions builds new Docker image
3. **Image Registry:** Image pushed to GitHub Container Registry (ghcr.io)
4. **Infrastructure Update:** Image tag updated in k8s-infra repository
5. **ArgoCD Sync:** ArgoCD detects changes and deploys automatically

## 🚀 Deployment

### Prerequisites
- Kubernetes cluster (v1.24+)
- ArgoCD installed in the cluster
- CloudNative PostgreSQL operator
- Longhorn storage provisioner
- Cloudflare Tunnel (for external access)

### Quick Start with ArgoCD

1. **Deploy Demo Environment:**
```bash
kubectl apply -f Kubernetes/demo/argocd-application.yaml
```

2. **Deploy Examples Environment:**
```bash
kubectl apply -f Kubernetes/examples/argocd-application.yaml
```

### Manual Deployment

1. **Create Namespace:**
```bash
kubectl create namespace parentinfo
```

2. **Deploy PostgreSQL Cluster:**
```bash
kubectl apply -f Kubernetes/demo/CloudNativePostgreSQL/
```

3. **Create Secrets:**
```bash
# Adjust secrets based on your environment
kubectl apply -f Kubernetes/demo/parentinfo-app/Django-SealedSecret.yaml
kubectl apply -f Kubernetes/demo/parentinfo-app/Postgres-SealedSecret.yaml
kubectl apply -f Kubernetes/demo/parentinfo-app/R2-SealedSecret.yaml
kubectl apply -f Kubernetes/demo/parentinfo-app/RegistryCredentials-SealedSecret.yaml
```

4. **Deploy Application:**
```bash
kubectl apply -f Kubernetes/demo/parentinfo-app/
```

## 📁 Project Structure

```
ParentInfoSystem-Django/
├── Docker/                      # Docker configuration files
│   ├── Dockerfile              # Production Dockerfile
│   ├── Dockerfile.prod         # Optimized production build
│   ├── entrypoint-dev.sh       # Development entrypoint script
│   ├── entrypoint.sh           # Production entrypoint script
│   └── sample_data.json        # Sample data for testing
├── Kubernetes/                  # Kubernetes manifests
│   ├── demo/                   # Demo environment configuration
│   │   ├── argocd-application.yaml
│   │   ├── CloudNativePostgreSQL/
│   │   └── parentinfo-app/
│   └── examples/               # Example configuration templates
│       ├── argocd-application.yaml
│       ├── CloudNativePostgreSQL/
│       └── parentinfo-app/
└── parentinfo/                  # Django application
    ├── manage.py
    ├── activity/               # Activity management app
    ├── announcement/           # Announcement system app
    ├── core/                   # Core functionality and models
    ├── dashboard/              # Admin dashboard
    └── parentinfo/             # Django project settings
```

## 🔒 Security Features

- **Sealed Secrets:** Kubernetes secrets encrypted with Sealed Secrets
- **SSL/TLS:** Automatic SSL via Cloudflare proxy
- **Database Security:** Isolated PostgreSQL with CNPG operator
- **Role-Based Access:** Django's built-in permission system
- **Network Isolation:** Cloudflare Tunnel for secure external access

## 🌐 Networking

The application uses **Cloudflare Tunnel** to expose the Kubernetes service securely:

```
Internet → Cloudflare Edge → Cloudflare Tunnel → K8s Service → Application Pod
```

Benefits:
- ✅ No exposed ports or public IPs
- ✅ Automatic SSL/TLS termination
- ✅ DDoS protection via Cloudflare
- ✅ Access control and firewall rules

## 📊 Storage Architecture

### Database Storage (Longhorn)
- Persistent volumes for PostgreSQL data
- Automated backup and snapshot capabilities
- Distributed storage across cluster nodes

### Object Storage (Cloudflare R2)
- Static files (CSS, JavaScript, images)
- User-uploaded media files
- S3-compatible API
- Global CDN distribution

## 🌐 Live Demo

You can access the live version of the application here:  
🔗 [demo-parentinfo.ruchan.dev](https://demo-parentinfo.ruchan.dev)

> ⚠️ **Note:** The admin panel is disabled for security reasons.  
> Data is static and refreshed weekly.  
> Example student data is available in the `Docker/sample_data.json` file.

## 🛠️ Local Development

1. **Clone the repository:**
```bash
git clone https://github.com/ruchany13/ParentInfoSystem-Django.git
cd ParentInfoSystem-Django
```

2. **Create virtual environment:**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies:**
```bash
pip install -r requirements.txt
```

4. **Configure environment variables:**
```bash
export DEBUG=True
export SECRET_KEY='your-secret-key'
export DATABASE_URL='postgresql://user:password@localhost/parentinfo'
# Add other required environment variables
```

5. **Run migrations:**
```bash
cd parentinfo
python manage.py migrate
```

6. **Create superuser:**
```bash
python manage.py createsuperuser
```

7. **Run development server:**
```bash
python manage.py runserver
```

## 📝 Environment Variables

Required environment variables for the application:

```bash
# Django Settings
DEBUG=False
SECRET_KEY=your-django-secret-key
ALLOWED_HOSTS=demo-parentinfo.ruchan.dev

# Database
DATABASE_URL=postgresql://user:password@host:5432/dbname

# Cloudflare R2
AWS_ACCESS_KEY_ID=your-r2-access-key
AWS_SECRET_ACCESS_KEY=your-r2-secret-key
AWS_STORAGE_BUCKET_NAME=your-bucket-name
AWS_S3_ENDPOINT_URL=https://your-account-id.r2.cloudflarestorage.com
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is licensed under the terms specified in the LICENSE file.

---

## 👨‍💻 Developer Info

- **Application & Infrastructure:** Ruchan Yalçın
- **GitHub:** [@ruchany13](https://github.com/ruchany13)
- **README Generated by:** GitHub Copilot (AI Assistant)