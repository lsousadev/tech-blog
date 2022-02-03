theme documentation: https://github.com/jeffreytse/jekyll-theme-yat

commands to build blog:
sudo bundle && sudo jekyll serve

updating s3 bucket (from ~/projects/tech-blog):
aws s3 sync _site s3://lukenascimento.com/ --delete

todo:
- CDN + https + log bucket