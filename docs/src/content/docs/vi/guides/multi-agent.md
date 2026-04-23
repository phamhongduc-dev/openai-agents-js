---
title: Điều phối Agent
description: Phối hợp luồng giữa nhiều agent
---

Điều phối (orchestration) đề cập đến luồng agent trong ứng dụng của bạn. Agent nào chạy, theo thứ tự nào, và chúng quyết định điều gì xảy ra tiếp theo như thế nào? Có hai cách chính để điều phối agent:

> Đọc trang này sau [Quickstart](/openai-agents-js/vi/guides/quickstart) hoặc [hướng dẫn Agent](/openai-agents-js/vi/guides/agents#composition-patterns). Trang này nói về thiết kế workflow xuyên nhiều agent, không phải constructor `Agent`.

1. Cho phép LLM đưa ra quyết định: sử dụng trí tuệ của LLM để lên kế hoạch, suy luận, và quyết định các bước cần thực hiện.
2. Điều phối bằng code: xác định luồng agent thông qua code.

Bạn có thể kết hợp cả hai mẫu. Mỗi cách có đánh đổi riêng, mô tả bên dưới.

## Điều phối qua LLM

Agent là LLM được trang bị instruction, tool và handoff. Nghĩa là với tác vụ mở, LLM có thể tự lên kế hoạch cách giải quyết, dùng tool để thực hiện hành động và lấy dữ liệu, dùng handoff để ủy quyền cho agent con.

### Mẫu SDK cốt lõi

Trong Agents SDK, hai mẫu điều phối xuất hiện thường xuyên nhất:

| Mẫu | Cách hoạt động | Tốt nhất khi |
| --- | --- | --- |
| Agent dùng như tool | Agent manager giữ quyền kiểm soát cuộc hội thoại và gọi agent chuyên biệt qua `agent.asTool()`. | Bạn muốn một agent sở hữu câu trả lời cuối, kết hợp output từ nhiều chuyên gia, hoặc áp dụng guardrail chung tại một nơi. |
| Handoff | Triage agent định tuyến cuộc hội thoại tới chuyên gia, chuyên gia trở thành agent active cho phần còn lại của lượt. | Bạn muốn chuyên gia nói trực tiếp với người dùng, giữ prompt tập trung, hoặc dùng instruction/model khác nhau cho mỗi chuyên gia. |

Dùng **agent như tool** khi chuyên gia nên giúp với tác vụ con nhưng không nên tiếp quản cuộc hội thoại hướng người dùng.

Dùng **handoff** khi bản thân việc định tuyến là phần của workflow và bạn muốn chuyên gia được chọn sở hữu phần tiếp theo.

Bạn có thể kết hợp hai mẫu. Triage agent có thể handoff cho chuyên gia, và chuyên gia vẫn dùng agent khác như tool cho tác vụ con có giới hạn.

Chiến thuật quan trọng nhất:

1. Đầu tư vào prompt tốt. Nêu rõ tool nào có sẵn, cách dùng, và tham số nào cần tuân thủ.
2. Giám sát ứng dụng và cải tiến. Xem lỗi ở đâu, cải tiến prompt.
3. Cho phép agent tự xem xét và cải thiện. Ví dụ chạy trong vòng lặp, cho tự phê bình.
4. Có agent chuyên biệt giỏi một tác vụ, thay vì agent đa năng.
5. Đầu tư vào [eval](https://platform.openai.com/docs/guides/evals).

## Điều phối bằng code

Điều phối bằng code giúp tác vụ xác định và dự đoán được hơn về tốc độ, chi phí và hiệu suất. Các mẫu phổ biến:

- Dùng [structured output](https://developers.openai.com/api/docs/guides/structured-outputs) để sinh dữ liệu có cấu trúc mà code kiểm tra được.
- Nối chuỗi nhiều agent bằng cách biến output của agent này thành input của agent tiếp theo.
- Chạy agent thực hiện tác vụ trong vòng lặp `while` với agent đánh giá và phản hồi.
- Chạy nhiều agent song song, ví dụ qua `Promise.all`.

Chúng tôi có nhiều ví dụ tại [`examples/agent-patterns`](https://github.com/openai/openai-agents-js/tree/main/examples/agent-patterns).

## Hướng dẫn liên quan

- [Agent](/openai-agents-js/vi/guides/agents) cho mẫu kết hợp và cấu hình agent.
- [Tool](/openai-agents-js/vi/guides/tools#agents-as-tools) cho `agent.asTool()` và điều phối kiểu manager.
- [Handoff](/openai-agents-js/vi/guides/handoffs) cho ủy quyền giữa agent chuyên biệt.
- [Chạy Agent](/openai-agents-js/vi/guides/running-agents) cho `Runner` và điều khiển điều phối theo lần chạy.
- [Quickstart](/openai-agents-js/vi/guides/quickstart) cho ví dụ handoff đầy đủ tối thiểu.
