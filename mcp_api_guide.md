# MCP - API 연결 주의사항 📚

## 1️⃣ 보안 (API 키 관리)

### 환경변수 설정 방법

```bash
# ~/.zshrc 파일 열기
nano ~/.zshrc

# 아래 내용 추가
export API_KEY="your-api-key-here"

# 저장 후 적용
source ~/.zshrc



# .env 파일 생성
API_KEY=your-api-key-here
MODEL=gpt-4

# .env 파일 읽기
from dotenv import load_dotenv
import os

load_dotenv()
api_key = os.getenv("API_KEY")


# .gitignore에 반드시 추가!
echo ".env" >> .gitignore


# 2️⃣ 에러 처리
import time

def call_api(prompt):
    max_retry = 3
    retry_count = 0
    
    while retry_count < max_retry:
        try:
            response = api.call(prompt)
            return response
            
        except TimeoutError:
            print(f"⏱️ 시간초과! {retry_count+1}번째 재시도...")
            retry_count += 1
            time.sleep(2)
            
        except AuthenticationError:
            print("🔑 API 키를 확인하세요!")
            break
            
        except RateLimitError:
            print("⚠️ 잠시 후 다시 시도합니다...")
            time.sleep(60)
            
        except Exception as e:
            print(f"❌ 에러 발생: {e}")
            break
    
    return None



#3️⃣ 속도 제한 (Rate Limit)

import time

class APIManager:
    def __init__(self):
        self.call_count = 0
        self.max_calls = 60
        self.start_time = time.time()
    
    def call_api(self, prompt):
        if time.time() - self.start_time > 60:
            self.call_count = 0
            self.start_time = time.time()
        
        if self.call_count >= self.max_calls:
            wait_time = 60 - (time.time() - self.start_time)
            print(f"⏳ {wait_time:.0f}초 대기 중...")
            time.sleep(wait_time)
            self.call_count = 0
        
        self.call_count += 1
        return api.call(prompt)



#4️⃣ 프로토콜 호환성

import mcp

# 버전 확인
print(mcp.__version__)

# 버전별 분기
if mcp.__version__ >= "1.0.0":
    client = mcp.Client(api_key=api_key)
else:
    client = mcp.connect(api_key=api_key)


# 버전 확인
pip show mcp

# 최신 버전 업데이트
pip install --upgrade mcp



#5️⃣ 비용 모니터링

class CostMonitor:
    def __init__(self):
        self.total_tokens = 0
        self.max_tokens = 100000
    
    def check_and_call(self, prompt):
        if self.total_tokens >= self.max_tokens:
            print("💸 토큰 한도 초과! API 호출 중단!")
            return None
        
        response = api.call(prompt)
        used = response.usage.total_tokens
        self.total_tokens += used
        
        print(f"📊 사용 토큰: {self.total_tokens}/{self.max_tokens}")
        return response



#📋 최종 체크리스트
# 연결 전
   #API 키 환경변수 설정
   #.env 파일 .gitignore 추가
   #MCP/API 버전 확인
# 연결 중
   #에러 처리 코드 작성
   #속도 제한 관리
   #재시도 로직 구현
# 연결 후
   #사용량 모니터링
   #비용 한도 설정
   #로그 기록




