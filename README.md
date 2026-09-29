# FloodLens

One photo of a flooded street in, a depth estimate and a can-I-pass verdict out, plus a live map of recent reports.

Stack: CloudFront + S3 (site), API Gateway HTTP API, one Python Lambda, Amazon Bedrock (Claude vision), DynamoDB (48-hour TTL).
Photos are never stored. Coordinates are rounded to about 110 m.

## Deploy (about 15 minutes)

1. In the Bedrock console, enable access to a vision-capable Claude model. Copy its model or inference-profile ID.
2. Install the AWS SAM CLI and run `aws configure` (or use your agent's AWS connection).
3. From this folder:
```
sam build
sam deploy --guided --parameter-overrides ModelId=<your-model-id>
```
4. Upload the site using the `BucketName` and `DistributionId` from the stack outputs:
```
aws s3 sync web/ s3://<BucketName>/
aws cloudfront create-invalidation --distribution-id <DistributionId> --paths "/*"
```
5. Open `SiteUrl` in an incognito window on your phone. Set a location, upload a flood photo, and confirm a result and a map dot appear.

## Before you submit

- [ ] Live URL works in incognito, on mobile data
- [ ] Set an AWS Budgets alarm (public Bedrock endpoint). API throttling is already 5 req/s.
- [ ] Test 10+ real flood photos and record honest error numbers for the post
- [ ] Screenshots or logs showing your coding agent connected to AWS (the hackathon requires proof)
- [ ] Push this folder to GitHub, then paste the repo and live URLs into the form
- [ ] Add both required tags (`#social-good`, `#community`) in Builder Center
- [ ] Publish before October 2, 11:59 PM PDT (about 12:29 PM IST on October 3)

## Known limits

Depth thresholds in `src/app.py` (`LIMITS`) are conservative heuristics, not official guidance. Estimates come from one photo and a model, so the app shows a range and a confidence level.

## Evaluate accuracy

Open `notebooks/floodlens_eval.ipynb` (Jupyter, SageMaker Studio or Colab), add photos to `notebooks/eval_photos/` and true depths to `notebooks/ground_truth.csv`, and run all cells. Replace the two example rows first.
