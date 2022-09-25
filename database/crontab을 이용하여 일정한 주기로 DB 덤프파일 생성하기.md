# crontab을 이용하여 일정한 주기로 DB 덤프파일 생성하기
### 1. dump를 위한 스크립트 생성(/home/ubuntu/dump.sh)
```Shell
FILE_NAME=DB_`date "+%Y%m%d%H%M"`
docker exec ce90 /usr/bin/mysqldump -uroot --password=${PASSWORD} --all-databases > /home/ubuntu/dump/${FILE_NAME}.sql
```

### 2. 만든 sh 파일을 바탕으로 cron 일정 등록
- `systemctl status cron` : 크론탭 데몬 확인
- `select-editor` : 크론탭 편집기 선택
- `crontab -e` : 크론 편집
아래와 같은 일정을 등록해준다      
> 매일 6시에 /home/ubuntu/dump.sh 파일을 실행
```Shell
0 6 * * * sudo sh /home/ubuntu/dump.sh
```
- `crontab -l` : 저장된 크론탭 내용 확인

### 결과
![image](https://user-images.githubusercontent.com/84266499/192168033-f007ecbe-d750-4a53-9e8b-f9758d93168f.png)      
크론 일정 잘 등록됨       
![image](https://user-images.githubusercontent.com/84266499/192168048-61d63bcb-3c19-4826-9311-9708ac998a69.png)     
지정한 경로에 덤프파일 잘 생성됨
