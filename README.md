**Step 1: Create S3 Bucket**
Sign in to the AWS Console, navigate to S3, and click Create bucket. Enter a globally unique name (e.g., my-static-site-2025), select your preferred Region (e.g., Mumbai for India), and keep defaults like encryption enabled. Do not enable public access yet; create the bucket.
​

**Step 2: Enable Static Hosting**
In the bucket's Properties tab, scroll to Static website hosting and edit. Select Enable, set Index document to index.html, and optionally set Error document to error.html or 404.html. Note the Bucket website endpoint (e.g., my-static-site-2025.s3-website-ap-south-1.amazonaws.com) for CloudFront origin use.
​

**Step 3: Upload Website Files**
In the bucket's Objects tab, click Upload and add your HTML, CSS, JS, and assets. Ensure index.html is at the root. Set ACL to public-read if needed temporarily, but rely on policies/OAC for production.
​

**Step 4: Configure CloudFront Distribution**
Go to CloudFront console, click Create distribution. Set Origin domain to your S3 website endpoint from Step 2 (not the REST API endpoint). Under Origin access, choose Origin access control settings (recommended), create/select an OAC with Sign requests (recommended). Keep viewer protocol as Redirect HTTP to HTTPS.
​

**Step 5: Secure S3 with Bucket Policy**
In S3 bucket Permissions > Bucket policy > Edit, add this policy (replace YOUR-BUCKET-NAME and DISTRIBUTION-ID from CloudFront):

text
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "AllowCloudFrontServicePrincipal",
    "Effect": "Allow",
    "Principal": {
      "Service": "cloudfront.amazonaws.com"
    },
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*",
    "Condition": {
      "StringEquals": {
        "AWS:SourceArn": "arn:aws:cloudfront::YOUR-ACCOUNT-ID:distribution/YOUR-DISTRIBUTION-ID"
      }
    }
  }]
}
Save; this restricts access to CloudFront only.
​

**Step 6: Finalize CloudFront & Test**
In CloudFront settings, set Default root object to index.html, enable compression, and create. Wait 10-20 minutes for deployment (Status: Deployed). Test the CloudFront domain (e.g., d123456789.cloudfront.net); it serves globally cached content securely. For custom domain, add Route 53/CNAME and ACM SSL.
