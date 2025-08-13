# AWS VPC with Public & Private Subnets, NAT Gateway, and Two EC2 Instances

A hands-on lab that creates a VPC (10.0.0.0/16) with one **public** subnet (10.0.1.0/24) and one **private** subnet (10.0.2.0/24), an **Internet Gateway** for public egress, a **NAT Gateway** for private egress, and two Windows EC2 instances (one in each subnet). Region used in screenshots: **eu-north-1 (Stockholm)**.

---

## 🗺️ Architecture

```mermaid
flowchart LR
    Internet((Internet))
    subgraph VPC[my-vpc-vpc (10.0.0.0/16)]
        subgraph Public[Public Subnet (10.0.1.0/24)]
            EC2Pub[EC2 - public]
            NAT[NAT Gateway]
        end
        subgraph Private[Private Subnet (10.0.2.0/24)]
            EC2Priv[EC2 - private]
        end
        IGW[Internet Gateway]
        RTpub[Public Route Table\n0.0.0.0/0 -> IGW]
        RTpriv[Private Route Table\n0.0.0.0/0 -> NAT]
    end

    Internet <---> IGW
    EC2Pub --- RTpub
    NAT --- Public
    EC2Priv --- RTpriv
    RTpub <---> IGW
    RTpriv --> NAT
```

---

## ✅ What you build

- 1 VPC (10.0.0.0/16)
- 2 subnets: **PUBLIC** (10.0.1.0/24) and **PRIVATE** (10.0.2.0/24)
- 2 route tables: **public** (default route to IGW) and **private** (default route to NAT)
- 1 Internet Gateway, attached to the VPC
- 1 NAT Gateway in the **public** subnet (with a new Elastic IP)
- 2 Security Groups:
  - **public-sg**: allow RDP (3389), HTTP(80)/HTTPS(443), SSH(22) from `0.0.0.0/0`
  - **private-sg**: allow RDP **from** `public-sg` only
- 2 EC2 instances (Windows t3.micro): one in the public subnet, one in the private subnet

---

## 🧭 Step-by-step (Console)

1. **Create VPC.**
2. **Create subnets**: one public and one private in different AZs with different CIDR blocks.
3. **Create Internet Gateway** and attach it to the VPC.
4. **Create route tables**: one **public**, one **private**; associate the correct subnets.
5. **Edit only the public route table** to add `0.0.0.0/0` -> IGW. (Leave private as-is.)
6. **Create security groups**:
   - *public-sg*: inbound RDP, HTTP/HTTPS, SSH from anywhere.
   - *private-sg*: inbound allowed **from** `public-sg`.
7. **Launch two EC2 instances** (Windows): one in public subnet with public IP, one in private subnet without public IP.
8. **RDP into the public instance** and confirm internet access.
9. **Create a NAT Gateway** (allocate new Elastic IP) **in the public subnet**; update the **private** route table with `0.0.0.0/0` -> NAT.
10. **RDP from the public instance to the private instance** using its private IP and verify internet connectivity from the private instance via the NAT.

> The original step list is available in [`steps/VPC_Steps.txt`](steps/VPC_Steps.txt).

---

## 🖼️ Screenshots

![AWS VPC Project Steps](https://github.com/karthickraja12m/aws-vpc-public-private-ec2-nat/blob/main/aws_project_steps_vertical.jpg?raw=true)




---

## 🧹 Cleanup

See [`CLEANUP.md`](CLEANUP.md) to remove all resources (terminate EC2, delete NAT Gateway & EIP, detach/delete IGW, delete route tables, subnets, and VPC).

---

## 💡 Notes

- NAT Gateway incurs hourly and data processing charges; delete it when done.
- You can convert this setup to Infrastructure as Code later with Terraform/CloudFormation.
- Tested from a Windows public jump host; you can also use AWS Systems Manager Session Manager to avoid opening SSH/RDP to the world.

---

## 🚀 How to push this repo to GitHub

```bash
# 1) Initialize git and commit
git init
git add .
git commit -m "AWS VPC: Public+Private Subnets, NAT, and two EC2 (Windows)"

# 2) Create a new GitHub repo (replace <YOUR_GH_USERNAME>)
git branch -M main
git remote add origin https://github.com/<YOUR_GH_USERNAME>/aws-vpc-public-private-ec2-nat.git
git push -u origin main
```

---

### Author
Karthickraja M — Final-year AI & DS student and aspiring DevOps Engineer.
