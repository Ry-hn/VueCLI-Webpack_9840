<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>

        <v-card>
            <v-card-title>
                <v-text-field v-model="search" append-icon="mdi-magnify" 
                label="search" single-line hide-details></v-text-field>
                <v-spacer></v-spacer>
                <v-btn color="red" dark @click="todoSelesai = true" style="margin-right: 10px">TODO SELESAI</v-btn>
                <v-btn color="success" dark @click="dialog = true">Tambah</v-btn>
            </v-card-title>
            <v-data-table :headers="headers" :items="todos" :search="search">
                <template v-slot:[`item.priority`]="{ item }">
                    <v-card v-if="item.priority == 'Penting'" style="border-color: red; color: red; width: 60px; text-align:center" outlined>
                        {{ item.priority }}
                    </v-card>
                    <v-card v-else-if="item.priority == 'Biasa'" style="border-color: blue; color: blue; width: 60px; text-align:center" outlined>
                        {{ item.priority }}
                    </v-card>
                    <v-card v-else outlined style="border-color: lightgreen; color: green; width: 100px; text-align:center">
                        {{ item.priority }}
                    </v-card>
                </template>

                <template  v-slot:[`item.actions`]="{ item }">
                    <v-icon small class="mr-2" @click="editItem(item), edit = true, dialog = true " style="color: blue">{{icons.mdiPencil}}</v-icon>
                    <v-icon small class="mr-2" @click="deleteItem(item), del=true" style="color: red">{{icons.mdiDelete}}</v-icon>
                </template>
            </v-data-table>
        </v-card>

    <v-dialog v-model="dialog" persistent max-width="600px">
        <v-card>
            <v-card-title>
                <span class="headline">FORM TODO</span>
            </v-card-title>
            <v-card-text>
                <v-container>
                    <v-text-field v-model="formTodo.task" label="Task" required></v-text-field>
                    <v-select v-model="formTodo.priority" :items="['Penting', 'Biasa', 'Tidak penting']" label="Priority" required></v-select>

                    <v-textarea v-model="formTodo.note" label="Note" required></v-textarea>
                </v-container>
            </v-card-text>
            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="cancel">CANCEL</v-btn>
                <v-btn color="blue darken-1" text @click="save">SAVE</v-btn>
                
            </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="del" max-width="600px">
        <v-card>
            <v-card-title>
                <span class="headline">Yakin Ingin Hapus?</span>
            </v-card-title>
            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="green darken-1" text @click="cancel">TIDAK</v-btn>
                <v-btn color="red darken-1" text @click="hapus()">YA</v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="todoSelesai" max-width="600px">
        <v-card>
            <v-card-title>
                <span class="headline">Finised To do</span>
            </v-card-title>
            <v-card-action>
                <v-data-table :headers="headerSelesai" :items="todos2" :search="search">
                    <template v-slot:[`item.priority`]="{ item }">
                         <v-card v-if="item.priority == 'Penting'" style="border-color: red; color: red; width: 60px; text-align:center" outlined>
                            {{ item.priority }}
                        </v-card>
                        <v-card v-else-if="item.priority == 'Biasa'" style="border-color: blue; color: blue; width: 60px; text-align:center" outlined>
                            {{ item.priority }}
                        </v-card>
                        <v-card v-else outlined style="border-color: lightgreen; color: green; width: 100px; text-align:center">
                            {{ item.priority }}
                        </v-card>
                    </template>
                </v-data-table>
            </v-card-action>
        </v-card>
    </v-dialog>

    </v-main>
</template>

<script>

import {
    mdiPencil,
    mdiDelete,
} from '@mdi/js'

export default {
    name:"ListUGD",
    data() {
        return {
            search: null,
            index: null,
            edit: false,
            del: false,
            dialog: false,
            todoSelesai: false,
            headers: [
                {
                    text:"Task",
                    align:"start",
                    sortable:true,
                    value:"task"
                },
                { text:"Priority", value: "priority"},
                { text:"Note", value: "note"},
                { text: "Actions", value:"actions"}
            ],
            headerSelesai: [
                 {
                    text:"Task",
                    align:"start",
                    sortable:true,
                    value:"task"
                },
                { text:"Priority", value: "priority"},
                { text:"Note", value: "note"},
            ],
            todos:[
                {
                    task:"bernafas",
                    priority:"Penting",
                    note:"huffttt"
                },
                {
                    task:"nongkrong",
                    priority:"Tidak penting",
                    note:"bersama teman - teman"
                },
                 {
                    task:"masak",
                    priority:"Biasa",
                    note:"masak air 500ml"
                },
            ],
            todos2: [],
            formTodo: {
                task:null,
                priority:null,
                note:null
            },
            icons: {
                mdiPencil,
                mdiDelete,
            }
        };
    },
    methods: {
        save() {
            if(!this.edit) 
                this.todos.push(this.formTodo);
            else {
                this.todos[this.index].task = this.formTodo.task
                this.todos[this.index].priority = this.formTodo.priority
                this.todos[this.index].note = this.formTodo.note

                // this.todos[this.index] = this.formTodo; data berhasil keganti tapi ui nda ke update ???
                this.edit = false;
            }
            this.resetForm();
            this.dialog = false;
        },
        cancel() {
            this.resetForm();
            this.edit = false;
            this.dialog = false;
            this.del = false;
        },
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null
            };
        },
        editItem(item) {
            this.formTodo = {
                task: item.task,
                priority: item.priority,
                note: item.note
            }
            this.index = this.cariIndex(item);
            this.edit = true;
        },
        deleteItem(item) {
            this.index = this.cariIndex(item);
        },
        cariIndex(item) {
            return this.todos.findIndex(todo => todo.task === item.task);
        },
        hapus() {
            this.del = false;
            this.todos2.push(this.todos[this.index]);
            this.todos.splice(this.index, 1);
        }
    }
}
</script>