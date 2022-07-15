1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
137
<template>
  <div class="modal is-active">
    <div class="modal-background"></div>
    <div class="modal-content">
      <form @submit.prevent="updateTask">
          <h1>Edit Modal</h1>
        <div class="field">
          <label class="label">Task Title</label>
          <div class="control">
            <input
              v-model="title"
              class="input"
              type="text"
              placeholder="Enter task"
            />
          </div>
        </div>
        <div class="field">
          <label class="label">Description</label>
          <div class="control">
            <textarea
              v-model="description"
              class="textarea"
              placeholder="Textarea"
            ></textarea>
          </div>
        </div>
        <div class="field">
          <label class="label">Assigned By</label>
          <div class="control">
            <input
              v-model="createdBy"
              class="input"
              type="text"
              placeholder="Enter Assigner's name"
            />
          </div>
        </div>
        <div class="field">
          <label class="label">Assigned To</label>
          <div class="control">
            <input
              v-model="assignedTo"
              class="input"
              type="text"
              placeholder="Enter task creator's name"
            />
          </div>
        </div>
        <div class="field is-grouped">
          <div class="control">
            <button type="submit" class="button is-link">Submit</button>
          </div>
          <div class="control">
            <button class="button is-link is-light">Cancel</button>
          </div>
        </div>
      </form>
    </div>
    <button
      class="modal-close is-large"
      @click="closeModal"
      aria-label="close"
    ></button>
  </div>
</template>
<script lang="ts">
import { defineComponent, reactive, toRefs, computed, onMounted } from 'vue'
import { useStore } from '@/store'
import { TaskItem } from '@/store/state'
import { MutationType } from '@/store/mutations'
export default {
  name: 'EditModal',
  props: {
    id: { type: Number, required: true }
  },
  setup (props: any) {
    const state = reactive({
      title: '',
      description: '',
      createdBy: '',
      assignedTo: ''
    })
    const store = useStore()
    const getTaskById = computed(() => store.getters.getTaskById(Number(props.id)))

    const setFields = () => {
      const task = store.getters.getTaskById(Number(props.id))
      if (task) {
        state.title = task.title
        state.createdBy = task.createdBy
        state.assignedTo = task.assignedTo
        state.description = task.description
      }
    }
    onMounted(() => { setFields() })
    const updateTask = () => {
      if (
        state.title === '' ||
        state.description === '' ||
        state.createdBy === '' ||
        state.assignedTo === ''
      ) {
        return
      }
      const task: TaskItem = {
        id: props.id,
        title: state.title,
        description: state.description,
        createdBy: state.createdBy,
        assignedTo: state.assignedTo,
        completed: false,
        editing: false
      }
      store.commit(MutationType.UpdateTask, task)
      state.title = ''
      state.createdBy = ''
      state.assignedTo = ''
      state.description = ''
    }
    const closeModal = () => {
      store.commit(MutationType.SetEditModal, { showModal: false, taskId: undefined })
    }
    return { closeModal, ...toRefs(state), updateTask }
  }
}
</script>
<style scoped>
label {
    color: #ffffff;
}
h1 {
    color: #ffffff;
    text-align: center;
    font-size: 2rem;
    margin-bottom: 3rem;
}
</style>
