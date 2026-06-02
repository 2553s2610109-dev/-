import streamlit as st
from google import genai

# ---------------------------
# 페이지 설정
# ---------------------------
st.set_page_config(
    page_title="연애상담 챗봇",
    page_icon="💕",
    layout="centered"
)

st.title("💕 연애상담 챗봇")
st.caption("Gemini 2.5 Flash Lite 기반")

# ---------------------------
# API 키 불러오기
# ---------------------------
try:
    api_key = st.secrets["GEMINI_API_KEY"]
except Exception:
    st.error("GEMINI_API_KEY가 설정되지 않았습니다.")
    st.stop()

# ---------------------------
# Gemini 클라이언트
# ---------------------------
try:
    client = genai.Client(api_key=api_key)
except Exception as e:
    st.error(f"Gemini 초기화 실패: {e}")
    st.stop()

# ---------------------------
# 채팅 기록 저장
# ---------------------------
if "messages" not in st.session_state:
    st.session_state.messages = [
        {
            "role": "assistant",
            "content": (
                "안녕하세요 😊\n\n"
                "연애 고민, 썸, 이별, 재회, 고백, 연락 문제 등 "
                "무엇이든 편하게 이야기해주세요."
            )
        }
    ]

# 기존 대화 표시
for msg in st.session_state.messages:
    with st.chat_message(msg["role"]):
        st.markdown(msg["content"])

# ---------------------------
# 사용자 입력
# ---------------------------
prompt = st.chat_input("고민을 입력하세요...")

if prompt:
    # 사용자 메시지 저장
    st.session_state.messages.append(
        {"role": "user", "content": prompt}
    )

    with st.chat_message("user"):
        st.markdown(prompt)

    # 답변 생성
    with st.chat_message("assistant"):
        try:
            with st.spinner("생각 중..."):

                # 최근 대화 기록 활용
                history_text = ""

                for m in st.session_state.messages[-20:]:
                    role = "사용자" if m["role"] == "user" else "상담사"
                    history_text += f"{role}: {m['content']}\n"

                system_prompt = """
너는 따뜻하고 공감 능력이 높은 연애상담 전문가다.

규칙:
- 상대를 존중하는 방향으로 조언한다.
- 단정적으로 판단하지 않는다.
- 공감 → 상황 분석 → 현실적인 조언 순서로 답변한다.
- 한국어로 답변한다.
- 너무 길지 않게 답변한다.
"""

                full_prompt = f"""
{system_prompt}

대화 기록:
{history_text}

사용자 최신 질문:
{prompt}
"""

                response = client.models.generate_content(
                    model="gemini-2.5-flash-lite",
                    contents=full_prompt
                )

                answer = response.text

                st.markdown(answer)

                st.session_state.messages.append(
                    {
                        "role": "assistant",
                        "content": answer
                    }
                )

        except Exception as e:
            error_msg = f"오류가 발생했습니다.\n\n{str(e)}"

            st.error(error_msg)

            st.session_state.messages.append(
                {
                    "role": "assistant",
                    "content": error_msg
                }
            )

# ---------------------------
# 사이드바
# ---------------------------
with st.sidebar:
    st.header("설정")

    if st.button("대화 초기화"):
        st.session_state.messages = [
            {
                "role": "assistant",
                "content": "새로운 상담을 시작할게요 😊"
            }
        ]
        st.rerun()
