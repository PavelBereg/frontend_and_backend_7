<template>
  <!-- ХЕДЕР CodeStart -->
  <nav class="navbar has-shadow is-link mb-5" role="navigation" aria-label="main navigation">
    <div class="container">
      <div class="navbar-brand">
        <a class="navbar-item has-text-weight-bold is-size-4" href="#">
          CodeStart: Панель управления
        </a>
      </div>
    </div>
  </nav>

  <main class="container px-4">
    <h1 class="title is-2 has-text-centered mb-6">Каталог курсов</h1>

    <div class="columns">
      <!-- ЛЕВАЯ КОЛОНКА: Форма добавления нового курса -->
      <div class="column is-4">
        <div class="box">
          <h2 class="title is-4">Добавить курс</h2>
          <form @submit.prevent="addCourse">
            
            <div class="field">
              <label class="label">Название курса</label>
              <div class="control">
                <input v-model="newCourse.title" class="input" type="text" placeholder="Например: Основы React" required>
              </div>
            </div>

            <div class="field">
              <label class="label">Описание</label>
              <div class="control">
                <textarea v-model="newCourse.description" class="textarea" placeholder="Чему научится студент..." rows="3" required></textarea>
              </div>
            </div>

            <div class="field">
              <label class="label">URL Обложки (Фото)</label>
              <div class="control">
                <input v-model="newCourse.photo" class="input" type="url" placeholder="https://ссылка-на-картинку.jpg" required>
              </div>
            </div>

            <button class="button is-link is-fullwidth mt-4" type="submit">Добавить в каталог</button>
          </form>
        </div>
      </div>

      <!-- ПРАВАЯ КОЛОНКА: Список курсов и сортировка -->
      <div class="column is-8">
        
        <!-- Панель сортировки -->
        <div class="is-flex is-justify-content-space-between is-align-items-center mb-4">
          <p class="subtitle is-5 m-0">Всего курсов: <strong>{{ courses.length }}</strong></p>
          <div class="select is-link is-outlined">
            <select v-model="sortOrder">
              <option value="newest">Сначала новые</option>
              <option value="oldest">Сначала старые</option>
              <option value="titleAsc">По алфавиту (А-Я)</option>
              <option value="titleDesc">По алфавиту (Я-А)</option>
            </select>
          </div>
        </div>

        <!-- Сетка карточек с курсами -->
        <div class="columns is-multiline">
          
          <div v-if="sortedCourses.length === 0" class="column is-12 has-text-centered has-text-grey mt-5">
            <p>Каталог пуст. Добавьте первый курс!</p>
          </div>

          <div v-for="course in sortedCourses" :key="course.id" class="column is-6">
            <div class="card is-flex is-flex-direction-column" style="height: 100%;">
              <div class="card-image">
                <figure class="image is-2by1">
                  <img :src="course.photo" alt="Обложка курса" style="object-fit: cover;">
                </figure>
              </div>
              <div class="card-content is-flex-grow-1">
                <p class="title is-4">{{ course.title }}</p>
                <p class="content has-text-grey">{{ course.description }}</p>
              </div>
              <div class="card-footer">
                <a href="#" @click.prevent="removeCourse(course.id)" class="card-footer-item has-text-danger">
                  Удалить курс
                </a>
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>
  </main>

  <!-- ФУТЕР CodeStart -->
  <footer class="footer has-background-dark has-text-white mt-6">
    <div class="content has-text-centered">
      <p>© 2026 CodeStart Inc. Каталог на базе Vue 3.</p>
    </div>
  </footer>
</template>

<script setup>
import { ref, computed } from 'vue'

// 1. Исходные данные (наши старые курсы из 8 практики)
const courses = ref([
  {
    id: 1,
    title: 'Python-разработчик',
    description: 'Самый популярный язык для старта. Создавайте нейросети, телеграм-ботов и сайты.',
    photo: 'https://images.unsplash.com/photo-1526374965328-7f61d4dc18c5?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80',
    date: 1
  },
  {
    id: 2,
    title: 'Frontend (JS + React)',
    description: 'Создавайте красивые и интерактивные интерфейсы. HTML, CSS, JavaScript и React.',
    photo: 'https://images.unsplash.com/photo-1593720213428-28a5b9e94613?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80',
    date: 2
  }
])

// 2. Состояние формы
const newCourse = ref({
  title: '',
  description: '',
  photo: ''
})

// Состояние сортировки
const sortOrder = ref('newest')

// 3. Функции добавления и удаления
const addCourse = () => {
  courses.value.push({
    id: Date.now(),
    title: newCourse.value.title,
    description: newCourse.value.description,
    photo: newCourse.value.photo,
    date: Date.now() // для сортировки по новизне
  })
  
  // Очистка формы после добавления
  newCourse.value = { title: '', description: '', photo: '' }
}

const removeCourse = (id) => {
  courses.value = courses.value.filter(course => course.id !== id)
}

// 4. Вычисляемое свойство для сортировки (динамически обновляет сетку)
const sortedCourses = computed(() => {
  // Копируем массив, чтобы не мутировать оригинал
  let sorted = [...courses.value]
  
  if (sortOrder.value === 'newest') {
    sorted.sort((a, b) => b.date - a.date)
  } else if (sortOrder.value === 'oldest') {
    sorted.sort((a, b) => a.date - b.date)
  } else if (sortOrder.value === 'titleAsc') {
    sorted.sort((a, b) => a.title.localeCompare(b.title))
  } else if (sortOrder.value === 'titleDesc') {
    sorted.sort((a, b) => b.title.localeCompare(a.title))
  }
  
  return sorted
})
</script>

<style>
/* Микро-анимация появления карточек */
.card {
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}
.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0,0,0,0.1);
}
</style>