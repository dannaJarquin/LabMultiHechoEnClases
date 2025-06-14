<template>
  <div class="p-6 bg-teal-100 min-h-screen">
    <div class="overflow-x-auto">
      <template v-if="projectsStore.selectedProject">
        <h2 class="text-xl font-bold mb-4">
          Tareas de: {{ projectsStore.selectedProject.name }}
        </h2>

        <table class="table w-full">
          <thead>
            <tr>
              <th>#</th>
              <th>Nombre</th>
              <th>Completado</th>
              <th>Fecha de completado</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(task, index) in projectsStore.selectedProject.tasks"
              :key="task.id"
            >
              <td>{{ index + 1 }}</td>
              <td>{{ task.name }}</td>
              <td>
                <input
                  type="checkbox"
                  class="checkbox checkbox-success"
                  :checked="task.completed"
                  @change="toggleTaskCompleted(task)"
                />
              </td>
              <td>{{ task.completedAt || '-' }}</td>
            </tr>
          </tbody>
        </table>

        <p
          v-if="projectsStore.selectedProject.tasks.length === 0"
          class="text-gray-500 mt-2"
        >
          Sin tareas
        </p>
        <div class="flex items-center gap-2 mt-4">
          <input
            v-model="newTaskName"
            type="text"
            placeholder="Nombre de la nueva tarea"
            class="input input-bordered w-full max-w-xs"
            @keyup.enter="addTask"
          />
          <button class="btn btn-primary" @click="addTask">Agregar</button>
        </div>

        <button class="btn btn-sm mt-4" @click="projectsStore.deselectProject">
          Volver
        </button>
      </template>

      <template v-else>
        <table class="table w-full">
          <thead>
            <tr>
              <th>#</th>
              <th>Nombre</th>
              <th>Tareas</th>
              <th>Avance</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(project, index) in projectsStore.projects"
              :key="project.id"
            >
              <th>{{ index + 1 }}</th>
              <td>{{ project.name }}</td>
              <td>{{ project.tasks.length }}</td>
              <td>
                <div class="flex items-center gap-2">
                  <progress
                    class="progress progress-info w-56"
                    :value="getProjectProgress(project)"
                    max="100"
                  ></progress>
                  <span>{{ getProjectProgress(project) }}%</span>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </template>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import { useProjectsStore } from "@/stores/projects";

const projectsStore = useProjectsStore();

const newTaskName = ref("");

function addTask() {
  const trimmed = newTaskName.value.trim();
  if (trimmed !== "") {
    projectsStore.addTaskToProject(projectsStore.selectedProjectId, trimmed);
    newTaskName.value = "";
  }
}

function getProjectProgress(project) {
  const tasks = project.tasks || [];
  const total = tasks.length;
  const completed = tasks.filter((t) => t.completed).length;
  return total === 0 ? 0 : Math.round((completed / total) * 100);
}

function toggleTaskCompleted(task) {
  task.completed = !task.completed;
  task.completedAt = task.completed ? new Date().toLocaleString() : null;
}

watch(
  () => projectsStore.selectedProjectId,
  (val) => {
    console.log("🧪 selectedProjectId cambió a:", val);
  }
);
</script>
