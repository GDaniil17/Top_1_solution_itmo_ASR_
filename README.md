# 🏆 Top 1 solution — ITMO ASR

### ASR - Numbers Recognition in Russian

[Ссылка на Kaggle соревнование](https://www.kaggle.com/competitions/asr-numbers-recognition-in-russian/leaderboard)

---

## 📊 Итоговые результаты

<img width="1188" alt="image" src="https://github.com/user-attachments/assets/f532358d-1e9f-4ef3-9b87-f5899519978a" />

| Модель                | Private Score 🔒 | Public Score 🌐 | Описание                                         |
|-----------------------|------------------|-----------------|--------------------------------------------------|
| `predictions (5).csv` | **16.765**       | **15.437**      | 📈 Дообучение на второй тетрадке (вероятно предел) |
| `predictions (4).csv` | 17.825           | 16.624          | 🌙 Ночное обучение                                 |
| `predictions (3).csv` | 21.692           | 19.519          | 🔻 Negative drop                                   |

> 💡 Финальное решение (`predictions (5).csv`) заняло **1 место в приватном лидерборде**.

---

## 🧠 Архитектура модели

- **Модель:** CNN ➡️ BiLSTM ➡️ Linear ➡️ `log_softmax`
- **Loss:** CTC Loss (на выход модели подаются `log_softmax` logits)
- **Количество параметров:**
  - Всего параметров: **2,818,635**
  - Обучаемых параметров: **2,818,635**

### Структура модели

#### CNN Encoder
- `Conv2d(1, 32, kernel_size=3, padding=1)` ➡️ ReLU ➡️ MaxPool2d(kernel_size=(2,1))
- `Conv2d(32, 64, kernel_size=3, padding=1)` ➡️ ReLU ➡️ MaxPool2d(kernel_size=(2,1))
- `Conv2d(64, 64, kernel_size=3, padding=1)` ➡️ ReLU ➡️ MaxPool2d(kernel_size=(2,1))

#### BiLSTM Head
- Input size: 1024 (`64 * 16`) — после CNN свёрток и `MaxPool`
- Hidden size: 192
- Layers: 2
- Bidirectional: ✅
- Dropout: 0.3

#### Classifier
- `Linear(384, vocab_size)`
- `log_softmax(dim=2)` внутри `forward`

---

## 🔄 Этапы обучения

### Основное обучение (`train_submission.ipynb`)
- Стандартное обучение с агрессивными аугментациями.
- Многочасовой ночной прогон.
- Early stopping по приватной метрике.

### Финальное дообучение (`aftertraining.ipynb`)
- Fine-tune на расширенном датасете (`вторая тетрадка`).
- Пониженный learning rate.
- Дообучение от checkpoint лучшей модели (`ckpts/model_finetuned_30ep.pth`).

---

## 📜 Файлы проекта

- `aftertraining.ipynb` — дообучение модели (финальный чекпоинт для `predictions (5).csv`)
- `train_submission.ipynb` — основное обучение модели (чекпоинт для `predictions (4).csv`)

