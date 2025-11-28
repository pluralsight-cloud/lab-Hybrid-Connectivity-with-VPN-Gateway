# lab-Hybrid-Connectivity-with-VPN-Gateway

This repo contains a six-step set of ARM templates that together stand up a small hybrid-style Azure network for lab and testing. After all objectives are deployed, the environment includes:

- Three virtual networks (`vnet1`, `vnet2`, `vnet3`) with multiple subnets, expanded address spaces, and NSG associations.
- VPN gateways in each VNet with public IPs, diagnostics streamed to Storage + Log Analytics, and BGP-enabled connections forming a mesh (vnet1↔vnet2↔vnet3).
- Additional VNet-to-VNet connections using shared keys for testing both non-BGP and BGP scenarios.
- Extra subnets added to each VNet to simulate growth and segmentation.
- Test Ubuntu VMs (one per VNet) placed in `subnet1`, with ICMP allowed in NSGs for connectivity checks and boot diagnostics written to a storage account.

Deploy the templates in order (`objective-01` through `objective-06`) to build up the full topology. Adjust parameters as needed for your region, names, address spaces, and credentials. Each objective folder contains its own README with resource details and deployment commands.
