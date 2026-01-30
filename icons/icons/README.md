# Cloud Provider Icons

This directory contains official product icons from Azure, AWS, and Google Cloud Platform for use in architecture diagrams and documentation.

## Directory Structure

```
icons/
├── azure/          # Microsoft Azure Architecture Icons
│   ├── Icons/      # 683 SVG icons organized by category
│   │   ├── ai + machine learning/
│   │   ├── analytics/
│   │   ├── app services/
│   │   ├── compute/
│   │   ├── containers/
│   │   ├── databases/
│   │   ├── networking/
│   │   ├── security/
│   │   └── ... (23 categories total)
│   ├── Azure_Icons_FAQ.pdf
│   └── Microsoft_Terms_of_Use.pdf
│
├── aws/            # AWS Architecture Service Icons
│   ├── Arch_Compute/
│   ├── Arch_Containers/
│   ├── Arch_Database/
│   ├── Arch_Networking-Content-Delivery/
│   ├── Arch_Security-Identity-Compliance/
│   ├── Arch_Artificial-Intelligence/
│   └── ... (24 categories total, 1236 icons)
│   └── Each category has 16/, 32/, 64/ subdirectories
│
└── gcp/            # Google Cloud Platform Product Icons
    ├── google_kubernetes_engine.svg
    ├── bigquery.svg
    ├── cloud_run.svg
    ├── vertex_ai.svg
    └── ... (216 icons total)
```

## Icon Counts

- **Azure**: 683 SVG icons
- **AWS**: 1,236 SVG icons (multiple sizes: 16px, 32px, 64px)
- **GCP**: 216 SVG icons

## Sources

### Microsoft Azure Icons
- **Official Source**: [Microsoft Learn - Azure Architecture Icons](https://learn.microsoft.com/en-us/azure/architecture/icons/)
- **Version**: V21 (Public Service Icons)
- **Direct Download**: https://arch-center.azureedge.net/icons/Azure_Public_Service_Icons_V21.zip
- **Format**: SVG (18x18px)
- **License**: See `Microsoft_Terms_of_Use.pdf`

### AWS Architecture Icons
- **Official Source**: [AWS Architecture Icons](https://aws.amazon.com/architecture/icons/)
- **Release Date**: July 31, 2025 (Q3 2025)
- **Format**: SVG (16px, 32px, 64px)
- **License**: AWS permits use in architectural diagrams, training materials, and documentation

### Google Cloud Platform Icons
- **Official Source**: [Google Cloud Icons](https://cloud.google.com/icons)
- **Repository**: [GitHub - AwesomeLogos/google-cloud-icons](https://github.com/AwesomeLogos/google-cloud-icons)
- **Format**: SVG
- **License**: Google Cloud Platform branding guidelines apply

## Usage in Architecture Diagrams

These icons are embedded in the enterprise architecture diagrams:

1. **azure-architecture-with-icons.html** - Uses icons from `azure/Icons/`
2. **aws-architecture-with-icons.html** - Uses 64px icons from `aws/Arch_*/64/`
3. **gcp-architecture-with-icons.html** - Uses icons from `gcp/`

## Finding Specific Icons

### Azure
Icons are organized by service category. Common locations:
- Networking: `azure/Icons/networking/` (Firewall, Bastion, VPN Gateway)
- Containers: `azure/Icons/containers/` (AKS, Container Instances)
- Databases: `azure/Icons/databases/` (Cosmos DB, SQL Database)
- AI/ML: `azure/Icons/ai + machine learning/` (Azure OpenAI, Azure ML)

### AWS
Icons are organized by service category with multiple sizes:
- Containers: `aws/Arch_Containers/64/` (EKS, ECS, ECR)
- Compute: `aws/Arch_Compute/64/` (EC2, Lambda)
- Database: `aws/Arch_Database/64/` (RDS, DynamoDB)
- Networking: `aws/Arch_Networking-Content-Delivery/64/` (ALB, Route 53)

### GCP
Icons use lowercase with underscores:
- `google_kubernetes_engine.svg` (GKE)
- `bigquery.svg` (BigQuery)
- `cloud_run.svg` (Cloud Run)
- `vertex_ai.svg` (Vertex AI)

## Recommended Icon Sizes

- **Azure**: 18x18px (native), scale to 48px for diagrams
- **AWS**: Use 64px version for best quality in diagrams
- **GCP**: SVG scales well, render at 48-64px for diagrams

## Usage Guidelines

### Microsoft Azure
- Use official Azure blue (#0078D4) as primary color
- Don't distort, rotate, or modify icon shapes
- Maintain clear space around icons
- See `Microsoft_Terms_of_Use.pdf` for complete terms

### AWS
- Use official AWS orange (#FF9900) for highlights
- Icons are designed for light backgrounds
- Don't modify icon artwork
- Updated quarterly (Q1, Q2, Q3)

### Google Cloud Platform
- Use Google's official color palette
- Icons work well on white or light backgrounds
- Maintain aspect ratios
- Follow GCP branding guidelines

## Last Updated

Icons downloaded: January 26, 2026
- Azure: V21 Public Service Icons
- AWS: Q3 2025 Release (July 31, 2025)
- GCP: Latest from official repository

## Notes

These icons are for use in this project's architecture documentation and diagrams. For redistribution or use outside this project, please review the respective license terms from each cloud provider.
