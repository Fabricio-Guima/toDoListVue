<template>
  <div id="app">
    <h1>Tarefas</h1>
    <TasksProgress :progress="progress" />
    <NewTask @taskAdded="addTask" />
    <TaskGrid :tasks="tasks" @task-deleted="deletedtask" @taskstatechanged="toggleTaskState" />
  </div>
</template>

<script>
import TaskGrid from "./components/TaskGrid";
import NewTask from "./components/NewTask";
import TasksProgress from "./components/TasksProgress";
export default {
  name: "App",
  components: { TaskGrid, NewTask, TasksProgress },
  data() {
    return {
      tasks: [],
    };
  },
  computed: {
    progress() {
      const total = this.tasks.length;
      const done = this.tasks.filter((t) => !t.pending).length;
      return Math.round((done / total) * 100) || 0;
    },
  },
  methods: {
    addTask(task) {
      const sameName = (t) => t.name === task.name;
      const reallyNew = this.tasks.filter(sameName).length == 0;
      if (reallyNew) {
        this.tasks.push({
          name: task.name,
          pending: task.peding || true,
        });
      }
    },
    deletedtask(i) {
      this.tasks.splice(i, 1);
    },
    toggleTaskState(i) {
      this.tasks[i].pending = !this.tasks[i].pending;
    },
  },
  watch: {
    tasks: {
      // deep = olhar as alteraçoes profundas de cada elemento q ta nesse array, por exemplo, ele olha se o css do elemento mudou
      deep: true,
      handler() {
        //converter para string = JSON.stringify
        localStorage.setItem("tasks", JSON.stringify(this.tasks));
      },
    },
  },
  created() {
    const json = localStorage.getItem("tasks");
    //transformar o que é string para json
    const array = JSON.parse(json);
    //verificar se a const array é array mesmo
    if (Array.isArray(array)) {
      this.tasks = JSON.parse(json);
    } else {
      this.tasks = [];
    }
  },
};
</script>

<style>
body {
  font-family: "Lato", sans-serif;
  background: linear-gradient(to right, rgb(22, 34, 42), rgb(58, 96, 115));
  color: #fff;
}

#app {
  display: flex;
  flex: 1;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

#app h1 {
  margin-bottom: 5px;
  font-weight: 300;
  font-size: 3rem;
}

.task-grid {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

.task-grid .task {
  margin: 10px;
}

.no-task {
  color: #aaa;
  font-size: 1.7rem;
}
</style>
