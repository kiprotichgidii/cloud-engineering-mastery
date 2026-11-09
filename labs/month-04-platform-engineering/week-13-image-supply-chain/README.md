# Week 13: Advanced image build and supply-chain pipeline

[← Back to Month 4: Container Platforms and Application Delivery](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 13.

## Concepts to Investigate
- BuildKit architecture and cache model.
- Reproducible builds.
- Multi-platform builds.
- Private registry authentication.
- SBOM generation.
- Image signing and provenance.
- Artifact promotion and digest pinning.

## Hands-on Laboratory Modules
1. [**Lab 01 Multi Service Build**](lab-01-multi-service-build/README.md)
2. [**Lab 02 Ci Pipeline Sbom**](lab-02-ci-pipeline-sbom/README.md)
3. [**Lab 03 Image Signing Cosign**](lab-03-image-signing-cosign/README.md)
4. [**Lab 04 Immutable Promotion**](lab-04-immutable-promotion/README.md)

### Detailed Curriculum Specifications
1. Build a multi-service application with optimized multi-stage images and reproducible build inputs.
2. Create a CI pipeline with tests, BuildKit caching, SBOM generation and vulnerability scanning.
3. Sign image digests and verify signatures before promotion.
4. Promote the same immutable artifact between development, staging and production-like environments.

## Blog Article Target
**“Building a Secure Container Supply Chain with BuildKit, SBOMs and Cosign”**
