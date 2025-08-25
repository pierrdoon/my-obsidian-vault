Хорошо, давай соберём пример проекта на Vue 3 + TypeScript с архитектурой FSD, где:
данные берём с API через axios,
состояние храним в Pinia,
UI рендерим через Vuetify.
Я сделаю минимальный рабочий пример, где мы получаем список пользователей с API и отображаем его в таблице Vuetify.

📂 Структура проекта (FSD)
src/ 
├─ app/ # Конфигурация приложения 
│ ├─ providers/ # Подключение Vuetify, Pinia и т.д. 
│ ├─ App.vue 
│ └─ main.ts 
├─ shared/ 
│ ├─ api/ # axios instance 
│ │ └─ axiosInstance.ts 
│ └─ ui/ # Общие UI-компоненты 
├─ entities/ 
│ └─ user/ # Сущность пользователя 
│ ├─ model/ # Store + types 
│ ├─ api/ # userApi.ts 
│ └─ ui/ # Компоненты сущности 
├─ pages/
│ └─ UsersPage/ 
│ ├─ ui/ 
│ │ └─ UsersPage.vue 
│ └─ index.ts 
└─ widgets/ 
└─ UsersTable/ 
├─ ui/ 
│ └─ UsersTable.vue 
└─ index.ts 

1️⃣ Конфигурация axios — 
```js
// src/shared/api/axiosInstance.ts
import axios from "axios"; export const api = axios.create({ baseURL: "https://jsonplaceholder.typicode.com", // тестовый API timeout: 5000, }); 
```

2️⃣ API для пользователей — 
```js
//src/entities/user/api/userApi.ts
import { api } from "@/shared/api/axiosInstance"; 
import type { User } from "../model/types"; 

export const fetchUsers = async (): Promise<User[]> => { 
	const { data } = await api.get<User[]>("/users"); 
	return data; 
}; 
```

3️⃣ Типы и store — 
```js
// src/entities/user/model/types.ts
export interface User { 
	id: number; 
	name: string; 
	email: string; 
} 

// src/entities/user/model/userStore.ts
import { defineStore } from "pinia"; 
import type { User } from "./types"; 
import { fetchUsers } from "../api/userApi"; 

interface UserState { 
	users: User[]; 
	loading: boolean; 
	error: string | null; 
} 

export const useUserStore = defineStore("user", { state: (): UserState => (
{ users: [], 
	loading: false,
	error: null, }), 
	actions: { async loadUsers() { 
		this.loading = true; 
		this.error = null; 
		try { 
			this.users = await fetchUsers(); 
		} catch (err) { 
			this.error = "Не удалось загрузить пользователей"; 
		} finally { 
			this.loading = false; } }, }, 
	}); 
```

4️⃣ Таблица пользователей — 
```js
//src/widgets/UsersTable/ui/UsersTable.vue
<template> 
	<v-card> 
		<v-card-title>
			Список пользователей
		</v-card-title> 
		<v-data-table :headers="headers" :items="users" :loading="loading" loading-text="Загрузка..." ></v-data-table> 
		<v-alert v-if="error" type="error">{{ error }}</v-alert> 
	</v-card> 
</template> 

<script setup lang="ts"> 
import { computed, onMounted } from "vue"; 
import { useUserStore } from "@/entities/user/model/userStore"; 

const store = useUserStore(); 
const headers = [ { text: "ID", value: "id" }, { text: "Имя", value: "name" }, { text: "Email", value: "email" }, ]; 
const users = computed(() => store.users); 
const loading = computed(() => store.loading); 
const error = computed(() => store.error); 

onMounted(() => { store.loadUsers(); }); </script> 
```

5️⃣ Страница — 
```js
//src/pages/UsersPage/ui/UsersPage.vue
<template> 
<v-container> 
	<UsersTable /> 
</v-container> 
</template> 

<script setup lang="ts"> 
import UsersTable from "@/widgets/UsersTable/ui/UsersTable.vue"; 
</script> 
```
6️⃣ Точка входа — 
```js
// src/app/main.ts
import { createApp } from "vue"; 
import App from "./App.vue"; 
import { createPinia } from "pinia"; 
import "vuetify/styles"; 
import { createVuetify } from "vuetify"; 
import * as components from "vuetify/components"; 
import * as directives from "vuetify/directives"; 

const vuetify = createVuetify({ components, directives, }); 
const app = createApp(App); 
app.use(createPinia()); 
app.use(vuetify); 
app.mount("#app"); 
```

7️⃣ Корневой компонент — 
```js
// src/app/App.vue
<template> 
	<v-app> 
		<UsersPage />
	</v-app> 
</template> 

<script setup lang="ts"> 
import UsersPage from "@/pages/UsersPage/ui/UsersPage.vue"; 
</script> 
```
⚡ Теперь при запуске (npm run dev) ты получишь страницу, которая с архитектурой FSD, использует axios, Pinia и Vuetify для получения и отображения данных из API.
Хочешь, я сделаю сразу готовый шаблон репозитория с этой структурой, чтобы можно было клонировать и сразу запускать?
Тогда всё будет работать из коробки.