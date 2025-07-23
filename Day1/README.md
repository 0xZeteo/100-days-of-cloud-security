---

# 🛡️ Day 1: Misconfigured S3 Bucket and IAM Policies – Detection and Fix

## 🎯 Objective

Understand how misconfigured S3 buckets and IAM policies can lead to security issues — and how to fix them using AWS IAM and S3 controls.

---

## 🧰 Tools Used

* AWS Console
* IAM
* S3
* (Optional) AWS CLI

---

## 🛠️ What I Did (Step-by-Step)

### 1️⃣ Created a New S3 Bucket

I created an S3 bucket named:

```
zeteo-cloudsec-demo
```

> *This bucket is used to simulate a real-world cloud misconfiguration scenario.*

---

### 2️⃣ Uploaded a Sample Sensitive File

I uploaded a file named `sensitive.txt` containing simulated confidential data. This would represent a common file you'd never want to expose publicly.

```plaintext
Top secret file. Do not share.
```

---

### 3️⃣ Simulated a Misconfiguration (Public Access)

To simulate a real-world security mistake:

* I unchecked the “Block all public access” setting during bucket creation.
* I manually edited the bucket permissions to make it **publicly accessible**.

✅ I tested it by opening the **Object URL** in a browser — and confirmed I could access it publicly.

---

### 4️⃣ Created a New IAM User with Full S3 Access

I created an IAM user called:

```
Kim (arn:aws:iam::872515249817:user/Kim)
```

Then I attached an **inline policy** that gave this user full access to S3:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "*"
    }
  ]
}
```

✅ I confirmed that Kim could access and interact with the S3 bucket and its objects.

---

### 5️⃣ Secured the Bucket (Applied Fix)

After the misconfiguration simulation, I applied the following fixes:

#### 🔒 a. Disabled Public Access

* I **enabled “Block all public access”** on the S3 bucket.
* This immediately removed the public-facing Object URL access.
* Reopening the URL resulted in an **Access Denied** XML error, confirming the fix.

#### ✅ b. Applied IAM-Based Bucket Policy

I added a new **bucket policy** to allow **only the IAM user (Kim)** to access the bucket:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::872515249817:user/Kim"
      },
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::zeteo-cloudsec-demo",
        "arn:aws:s3:::zeteo-cloudsec-demo/*"
      ]
    }
  ]
}
```

---

### 6️⃣ Enabled S3 Server Access Logging (Auditing)

To track all access and actions against the bucket:

* I **enabled S3 Server Access Logging**
* Chose a different S3 bucket to store logs (`cloudsec-audit-logs`)

This helps in auditing access patterns, including any unauthorized attempts.

---

## ✅ Outcome

At the end of this lab, I was able to:

* Simulate a critical misconfiguration (public S3 bucket)
* Test real-world implications (object was publicly accessible)
* Fix the misconfiguration using **least privilege IAM** principles
* Ensure that only trusted IAM users could access the bucket
* Enable audit logging for future investigations

---

---

## 🧠 Lessons Learned

* Public S3 buckets can expose sensitive data with just one misclick.
* The principle of **least privilege** is critical — don't give *more* than necessary.
* Always enable server logging for auditing and traceability.
* Never rely on default configurations. Double-check all permission layers (IAM + Bucket Policies + ACLs).
