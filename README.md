# Lesson 3 — Контейнеризація ML-сервісу

У цьому завданні ми:
- експортуємо модель **MobileNetV2** з `torchvision` у формат TorchScript (`.pt`),
- пишемо скрипт `inference.py` для передбачення топ-3 класів по зображенню,
- створюємо два варіанти Docker-образів: **fat** та **slim**,
- порівнюємо їх за розміром та складом.

---

## Структура проєкту
```
lesson-3/
├─ export_model.py
├─ inference.py
├─ imagenet_class_index.json
├─ requirements.txt
├─ Dockerfile.fat
├─ Dockerfile.slim
├─ report.md
└─ samples/ # тестові зображення
```

---

# Кроки запуску

## 1. Перейти у директорію проєкту
```
cd lesson-3
```
## 2. Додати тестове зображення
```
mkdir -p samples
curl -L -o samples/cat.jpg https://raw.githubusercontent.com/pytorch/hub/master/images/dog.jpg
```
## 3. Побудова Docker-образів

Fat-образ
```
docker build -f Dockerfile.fat -t lesson3-fat .
```

Slim-образ (multi-stage)
```
docker build -f Dockerfile.slim -t lesson3-slim .
```

## 4. Запуск інференсу
Fat
```
docker run --rm -v "$PWD/samples:/samples" lesson3-fat --image /samples/cat.jpg
```

Slim
```
docker run --rm -v "$PWD/samples:/samples" lesson3-slim --image /samples/cat.jpg
```

#Очікуваний результат:
```
Top predictions:
- Labrador_retriever: 0.62
- golden_retriever: 0.19
- flat-coated_retriever: 0.04
```
## 5. Порівняння образів

Перевір розмір та кількість шарів:
## Розмір
```
docker images | grep lesson3
```

## Кількість шарів
```
docker history lesson3-fat  | wc -l
docker history lesson3-slim | wc -l
```
## Деталі шарів
```
docker history --no-trunc lesson3-fat
docker history --no-trunc lesson3-slim
```
