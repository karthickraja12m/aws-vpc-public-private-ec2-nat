# Cleanup Guide

To avoid unnecessary charges, delete resources in the following order:

1. Terminate EC2 instances (public and private).
2. Delete the NAT Gateway (this releases the Elastic IP).
3. Detach and delete the Internet Gateway (if no subnets/RTs depend on it).
4. Delete custom route tables (ensure subnets are detached first).
5. Delete subnets (private, then public).
6. Delete the VPC.

> Note: NAT Gateway is billed per hour + per GB. Don’t forget to remove it after testing.