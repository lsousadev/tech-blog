jekyll docs https://jekyllrb.com/docs/
theme documentation: https://github.com/jeffreytse/jekyll-theme-yat
bucket website url: http://lucas-sousa.com.s3-website.us-west-1.amazonaws.com/

commands to build blog, test it, and upload to s3:
cd ~/projects/tech-blog
sudo bundle
sudo jekyll serve
aws s3 sync _site s3://lucas-sousa.com/ --delete

categories:
- programming (algorithms, scripting)
- operating systems
- networking
- devops
- cloud
- cybersecurity

todo:
- improve stats page (ref https://www.raymondcamden.com/2018/07/21/building-a-stats-page-for-jekyll-blogs)
- visitor count / analytics https://ravichaganti.com/blog/adding-visitor-counter-to-statically-generated-web-pages/ https://michaelsoolee.com/google-analytics-jekyll/