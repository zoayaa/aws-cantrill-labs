# IAM (Identity and Access Management) — Lab Documentation

## Objective  
Understand how AWS IAM works by:
- Creating IAM Users and Groups  
- Understanding IAM identities  
- Assigning policies (Managed and Inline)  
- Using IAM Roles  
- Practicing secure AWS access  
- Understanding the principle of least privilege  

---

## Architecture Diagram (Conceptual View)

###  IAM User → Group → Policy  

IAM User → belongs to → IAM Group → has → IAM Policy  

“IAM users belong to IAM groups, and policies are attached to groups (or directly to users).”

### IAM Role → EC2 → S3  

IAM Policy → attached to → IAM Role → assigned to → EC2 → accesses → S3  

“Policies are attached to IAM Roles, and these roles are assigned to EC2 instances (not to S3). Because of that role, EC2 can access S3.”

---

## 🧪 What I Built in AWS (Lab Summary)

I performed the following in the AWS Console:
- Created an IAM user  
- Created an IAM group  
- Attached policies to the group:
  - Managed Policy  
  - Inline Policy  
- Assigned user to the group  
- Created an IAM role with:
  - Trust policy  
  - Permission policy  

---

## 🚧 Challenges Faced

- Initially confused between **IAM User vs IAM Role**  
- Faced permission errors while trying to access S3  

---

## 💡 Learnings & Insights

- IAM is the foundation of AWS security.  
- Users are for humans; roles are for services and resources.  
- Always follow the **principle of least privilege** — give only required permissions.  
- Policies define what users, roles, and services can do in AWS.  
- IAM roles are preferred over hard-coded access keys for EC2 instances.  
- Never commit access keys to GitHub.  

---

## 🌍 Real-World Use Cases

In real organizations:
- Developers typically assume IAM roles instead of using permanent credentials.  
- EC2 instances use IAM roles instead of usernames/passwords or access keys.  
- S3 access is controlled via IAM policies attached to IAM roles.  
- *IAM Groups are NOT IAM identities.*
- You *cannot log in to IAM Groups* because they do not have credentials.  
