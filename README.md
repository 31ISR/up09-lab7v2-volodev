# Лабораторная работа №7

## Table of contents

1. [Инициализация проекта](#инициализация-проекта)
2. [Верстка Frontend'а](#верстка-frontendа)
3. [Написание REST API](#написание-rest-api)

---

# Музыкальный сервис

Разработайте фронтенд-приложение на Astro, используя данные с бэкенда на Django, выполняющего роль CMS и API-сервера.

## Функциональность

### Блог:

- Получение и отображение статей (запросы к Django REST API).
- Каждая статья содержит ???

## Технические заметки

- Фронтенд: Astro
- Бэкенд: Django + Django REST Framework (статьи создаются через админку и отдаются через API).

# Инициализация проекта

### Backend

1. Создайте виртуальную среду окружения в папке `.venv`

2. Установите зависимости

```bash
pip install django djangorestframework
```

3. Инициализируйте проект под названием `backend`

```bash
django-admin startproject backend
```

4. Создайте приложение под названием `api`

```bash
python manage.py startapp api
```

5. Создайте файл `requirements.txt`, где будут храниться пакеты python части проекта

```bash
pip freeze > requirements.txt
```

### Frontend

1. Создайте Astro проект

```bash
npm create astro@latest
```

| Промпт                  | Ответ                            |
| ----------------------- | -------------------------------- |
| Папка для проекта       | frontend                         |
| Шаблон                  | A basic, helpful starter project |
| Установить зависимости  | Да                               |
| Создать Git репозиторий | Нет                              |

2. Добавьте [CSS Reset](https://gist.githubusercontent.com/ktkv419/c8840dfcbcff24248c20f4199108b28e/raw/eec499022cca5ed984d91d2a2c2736d2aef8ed6d/reset.css)

### Связь проекта

1. Создайте Git репозиторий

2. Сделайте так, чтобы Git игнорировал папки `node_modules`, `.venv`, `dist` и `build`

3. Создайте коммит

# Верстка Frontend'а

[Референс Figma](https://www.figma.com/design/90mysqUZ9RjXM2yWJ0UV6N/UP09-LAB7?m=auto&t=9SGo9bpok6NpNNVv-1)

### Задача

Разработать layout блога

_Можете разработать полностью свой дизайн, но все UI элементы должны присутствовать_

#### Карточка

- Картинка
- Дата создания
- Название
- Описание
- Теги

#### Хэдер

- Ссылка блог
- Ссылка на "О нас"
- Тогл темной темы

_Доступ к элементам меню в мобильной версии должен быть через гамбургер меню_

#### Футер

#### Блог

Для всех статей стандартный двуколоночный лэйаут

Для последних статей сформированное в более креативном стиле (смотрите референс `Recent blog posts`)

### Основные концепции Astro

Astro — это современный фреймворк для создания быстрых, оптимизированных сайтов с минимальным количеством JavaScript. Основное преимущество: генерирует HTML на сервере и отправляет минимум JS клиенту.

## Структура проекта

```
my-astro-project/
├── public/              # Статические файлы (изображения, шрифты)
├── src/
│   ├── components/      # Многоразовые компоненты
│   ├── layouts/         # Шаблоны страниц
│   ├── pages/           # Страницы = маршруты
│   ├── styles/          # Глобальные стили
│   └── content/         # Коллекции контента (в формате Markdown/MDX)
├── astro.config.mjs     # Настройки Astro
└── package.json         # Зависимости проекта
```

## Роутинг

### На основе файловой системы

```
src/pages/
├── index.astro          # → / (главная страница)
├── about.astro          # → /about
├── blog/
│   ├── index.astro      # → /blog
│   └── post-1.astro     # → /blog/post-1
└── products/
    └── [id].astro       # → /products/:id (динамический маршрут)
```

### Динамические маршруты

```astro
---
// Файл: src/pages/products/[id].astro
export function getStaticPaths() {
  return [
    { params: { id: '1' } },
    { params: { id: '2' } },
    { params: { id: '3' } }
  ];
}

const { id } = Astro.params;
---

<h1>Товар ID: {id}</h1>
```

### Параметры запроса

```astro
---
// Доступ к URL-параметрам
const { searchParams } = Astro.url;
const query = searchParams.get('q');
---

<p>Вы искали: {query}</p>
```

## Компоненты Astro (.astro)

### Базовая структура компонента

```astro
---
// 1. Frontmatter — JavaScript/TypeScript код (серверный)
import OtherComponent from '../components/OtherComponent.astro';
const title = "Заголовок страницы";

// Пропсы компонента
const { color = "blue", size } = Astro.props;
---

<!-- 2. Шаблон компонента (HTML + выражения JSX) -->
<main>
  <h1 style={`color: ${color}; font-size: ${size}px;`}>{title}</h1>
  <OtherComponent />
  <slot /> <!-- Слот для вставки дочернего контента -->
</main>

<!-- 3. Стили компонента (автоматически изолированы) -->
<style>
  main {
    padding: 2rem;
    max-width: 1200px;
    margin: 0 auto;
  }

  h1 {
    font-weight: 700;
  }
</style>
```

### Использование компонентов

```astro
---
import Button from '../components/Button.astro';
import Card from '../components/Card.astro';
---

<Button type="primary" text="Нажми меня" />

<Card title="Заголовок карточки">
  <p>Это содержимое карточки, переданное через slot</p>
</Card>
```

### Именованные слоты

```astro
<!-- Компонент Layout.astro -->
<html>
  <head>
    <slot name="head" />
  </head>
  <body>
    <header>
      <slot name="header">Заголовок по умолчанию</slot>
    </header>

    <main>
      <slot /> <!-- Дефолтный слот -->
    </main>

    <footer>
      <slot name="footer" />
    </footer>
  </body>
</html>

<!-- Использование -->
<Layout>
  <meta slot="head" charset="utf-8" />
  <h1 slot="header">Мой сайт</h1>
  <p>Основной контент</p>
  <p slot="footer">© 2025 Мой сайт</p>
</Layout>
```

## Макеты (Layouts)

```astro
---
// src/layouts/BaseLayout.astro
const { title, description } = Astro.props;
---

<html lang="ru">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width" />
    <title>{title}</title>
    <meta name="description" content={description} />
    <link rel="icon" href="/favicon.ico" />
  </head>
  <body>
    <header>
      <nav>
        <a href="/">Главная</a>
        <a href="/about">О нас</a>
        <a href="/blog">Блог</a>
      </nav>
    </header>

    <main>
      <slot /> <!-- Здесь будет контент страницы -->
    </main>

    <footer>
      <p>© 2025 Мой сайт</p>
    </footer>
  </body>
</html>
```

## Стилизация

### Локальные стили (Scoped)

```astro
<style>
  /* Автоматически привязаны только к этому компоненту */
  .card {
    border: 1px solid #ddd;
    border-radius: 8px;
    padding: 16px;
  }
</style>
```

### Глобальные стили

```astro
<style is:global>
  /* Применяются глобально ко всему сайту */
  :root {
    --primary-color: #3498db;
    --secondary-color: #2ecc71;
  }

  body {
    font-family: 'Segoe UI', sans-serif;
    line-height: 1.6;
  }
</style>
```

### Импорт CSS файлов

```astro
---
// Глобальные стили
import '../styles/global.css';

// CSS модули (с уникальными именами классов)
import styles from '../styles/Card.module.css';
---

<div class={styles.container}>
  <h2 class={styles.title}>Заголовок</h2>
</div>
```

### Inline стили

```astro
---
const bgColor = "lightyellow";
const textColor = "darkblue";
---

<div style={`background-color: ${bgColor}; color: ${textColor};`}>
  Стилизованный контент
</div>
```

### CSS переменные

```astro
<div class="card">Контент с темой</div>

<style>
  .card {
    /* Использование CSS переменных */
    background: var(--card-bg, white);
    color: var(--card-text, black);
    border: 1px solid var(--card-border, #ddd);
    border-radius: var(--border-radius, 4px);
  }
</style>
```

## Полезные функции и директивы

### Условный рендеринг

```astro
---
const showFeature = true;
const user = { isAdmin: true, name: "Админ" };
---

{showFeature && <p>Функция включена</p>}

{user.isAdmin ? (
  <div class="admin-panel">Панель администратора для {user.name}</div>
) : (
  <div class="user-panel">Панель пользователя</div>
)}
```

### Работа с изображениями

```astro
---
import { Image } from 'astro:assets';
import localImage from '../assets/images/photo.jpg';
---

<!-- Оптимизированное изображение из локальных ресурсов -->
<Image
  src={localImage}
  alt="Описание изображения"
  width={600}
  height={400}
  format="webp"
/>

<!-- Удаленное изображение -->
<Image
  src="https://example.com/image.jpg"
  alt="Удаленное изображение"
  width={800}
  height={600}
/>
```

### Метаданные и SEO

```astro
---
import { SEO } from 'astro-seo';
---

<head>
  <SEO
    title="Заголовок страницы"
    description="Описание страницы для поисковых систем"
    openGraph={{
      basic: {
        title: "Мой сайт",
        type: "website",
        image: "https://mysite.com/og-image.jpg",
      }
    }}
    twitter={{
      creator: "@username"
    }}
    extend={{
      link: [{ rel: "icon", href: "/favicon.ico" }],
      meta: [
        { name: "keywords", content: "astro, веб-разработка, статический сайт" },
        { name: "author", content: "Ваше имя" }
      ],
    }}
  />
</head>
```

### Пагинация

```astro
---
// src/pages/blog/[page].astro
import { getCollection } from 'astro:content';

export async function getStaticPaths({ paginate }) {
  const posts = await getCollection('blog');
  const sortedPosts = posts.sort((a, b) => b.data.date - a.data.date);

  // Создание страниц с пагинацией по 10 постов на странице
  return paginate(sortedPosts, { pageSize: 10 });
}

const { page } = Astro.props;
// page.data = текущие записи
// page.url.prev = ссылка на предыдущую страницу (если есть)
// page.url.next = ссылка на следующую страницу (если есть)
---

<h1>Блог</h1>

<ul>
  {page.data.map(post => (
    <li><a href={`/blog/${post.slug}`}>{post.data.title}</a></li>
  ))}
</ul>

<nav>
  {page.url.prev ? <a href={page.url.prev}>Предыдущая</a> : null}
  {page.url.next ? <a href={page.url.next}>Следующая</a> : null}
</nav>
```

## Оптимизация и производительность

### Ленивая загрузка изображений

```astro
<img src="/images/large-photo.jpg" alt="Большое изображение" loading="lazy" />
```

### Responsive изображения

```astro
<picture>
  <source srcset="/images/photo-mobile.webp" media="(max-width: 600px)" type="image/webp">
  <source srcset="/images/photo-tablet.webp" media="(max-width: 1024px)" type="image/webp">
  <source srcset="/images/photo.webp" type="image/webp">
  <img src="/images/photo.jpg" alt="Адаптивное изображение" width="800" height="600">
</picture>
```

### Preloading важных ресурсов

```astro
<head>
  <!-- Предзагрузка критических ресурсов -->
  <link rel="preload" href="/fonts/main-font.woff2" as="font" type="font/woff2" crossorigin>
  <link rel="preload" href="/images/hero.webp" as="image">
</head>
```

### Рендер данных

### Локальные данные (JSON, YAML)

```astro
---
// Импорт локальных данных
import team from '../data/team.json';
import config from '../data/site.yaml';
---

<h1>{config.title}</h1>

<ul>
  {team.members.map(member => (
    <li>{member.name} - {member.role}</li>
  ))}
</ul>
```

_Данные потом будут подтягиваться с сервера, но пока используйте [эти](./data.json) как затычки_

## Написание REST API

### Добавьте API и DRF (Django Rest Framework) в Django приложение

Добавьте в массив `INSTALLED_APPS` внутри файла `settings.py` в корневом приложении Django части

### Добавьте модель `Article`

```python
from django.db import models
from django.contrib.auth.models import User

class Article(models.Model):
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='articles')
    image = models.ImageField(upload_to='articles/', blank=True, null=True)
    ???

    def __str__(self):
        return self.title
```

> [!WARNING]
> Модель должна содержать следующие поля
> |Название|Тип|
> |-|-|
> |title|Набор символов с ограничением в 200 символов|
> |desc|Текстовое поле|
> |content|Текстовое поле|
> |tags|Набор символов с ограничением в 255 символов (должен содержать подсказку (help_text), что это строка разделенная запятыми)|
> |created_at|Дата, время с флагом автосоздания (auto_now_add)|
> |author|Уже присутствует в сниппете выше|
> |image|Уже присутствует в сниппете выше|

### Добавьте место для храненения файлов

В конце файла `settings.py` добавьте 2 переменные

```python
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

_Не забудьте, что `os` это библиотека и её надо импортировать_

_Также для работы с картинками надо установить пакет `pillow`_

Добавьте медиа изображения в раздачу маршрутов сервера: в файле `api/urls.py` добавьте к массиву `urlspatters` статический маршрут

```python
...
from django.conf.urls.static import static
...

urlpatterns = [
    ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### Проведите миграции и добавьте форму создания статьи

#### Миграции

```bash
python manage.py makemigrations
python manage.py migrate
```

#### Создание формы

В файле `api/admin.py` привяжите модель `Article` к админке

```python
from .models import Article

admin.site.register(Article)
```

### Создайте админа

```bash
python manage.py createsuperuser
```

### Создание сериализатора

> [!INFO]
> Serializer в Django REST Framework (DRF) — это компонент, который превращает Python-объекты (например, модели Django) в JSON (или другой формат) и обратно.
> Нужен чтобы:
>
> - Преобразовать объекты из базы данных в JSON, чтобы отправить их на фронт (сериализация).
> - Проверить и обработать входящие JSON-данные, чтобы сохранить их как объекты моделей (десериализация и валидация).

Создайте файл `api/serializers.py`

```python
from rest_framework import serializers
from .models import Article

from rest_framework import serializers
from .models import Article

class ArticleListSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'desc', 'tags', 'created_at', 'author', 'image']

class ArticleViewSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = '__all__'
```

_`ArticleListSerializer` передает все статьи для формирования ленты (без контента), а `ArticleViewSerializer` передает только одну статью, но со всеми полями_

### Создайте представление получения всех статей

Внутри файла `api/views.py` добавьте функцию `ArticleViewSet`

```python
from rest_framework import viewsets
from .models import Article
from .serializers import ArticleSerializer

class ArticleViewSet(viewsets.ReadOnlyModelViewSet):
    queryset = Article.objects.all().order_by("-created_at")

    def get_serializer_class(self):
        if self.action == "retrieve":
            return ArticleViewSerializer
        return ArticleListSerializer
```

### Создайте маршрут для представления `ArticleViewSet`

Создайте файл `api/urls.py`

```python
from rest_framework.routers import DefaultRouter
from .views import ArticleViewSet
from django.urls import path, include

router = DefaultRouter()
router.register(r'articles', ArticleViewSet, basename='article')

urlpatterns = [
    path('', include(router.urls)),
]
```

Добавьте данный набор маршрутов в основное приложение

В файле `backend/urls.py`

```python
from django.urls import path, include

urlpatterns = [
    ...
    path('api/', include('api.urls')),
] + ...
```
