<template>
    <div class="flex justify-around my-4 md:h-full">
        <div class="flex flex-col gap-4">
            <!-- Add btn -->
            <div class="flex justify-end">
                <button
                    class="bg-blue-400 hover:bg-blue-500 text-white w-[96px] p-2 rounded-md"
                    @click="
                        openModal();
                        updateOrCreate = 'Create';
                    "
                >
                    Add
                </button>
            </div>
            <!-- Table -->
            <div>
                <table class="shadow-lg bg-gray-50 border-collapse">
                    <tr class="bg-gray-700 text-white text-center">
                        <th class="border px-8 py-4">ID</th>
                        <th class="border px-8 py-4">Account id</th>
                        <th class="border px-8 py-4">Arriving arabic name</th>
                        <th class="border px-8 py-4">Arriving english name</th>
                        <th class="border px-8 py-4">Sort</th>
                        <th class="border px-8 py-4">Actions</th>
                    </tr>
                    <tr v-for="el in recieveData" :key="el" class="text-center">
                        <td class="border px-8 py-4">{{ el.id }}</td>
                        <td class="border px-8 py-4" v-if="el.accountId">
                            {{ el.accountId }}
                        </td>
                        <td class="border px-8 py-4" v-else>-</td>
                        <td class="border px-8 py-4">{{ el.arrivingArabicName }}</td>
                        <td class="border px-8 py-4">{{ el.arrivingEnglishName }}</td>
                        <td class="border px-8 py-4">{{ el.sort }}</td>
                        <td class="border px-8 py-4 space-x-2">
                            <button
                                @click="
                                    updateOrCreate = 'update';
                                    fillModal(el);
                                "
                                class="bg-green-400 hover:bg-green-500 w-[80px] py-1 rounded-md text-white"
                            >
                                Edit
                            </button>
                            <button
                                @click="deleteArriving(el.id)"
                                class="bg-red-400 hover:bg-red-500 w-[80px] py-1 rounded-md text-white"
                            >
                                Delete
                            </button>
                        </td>
                    </tr>
                </table>
            </div>
            <!-- Pagination -->
            <div class="flex justify-center" v-if="totalItems > 10">
                <vue-awesome-paginate
                    :total-items="totalItems"
                    :items-per-page="rows"
                    :max-pages-shown="5"
                    v-model="currentPage"
                    :on-click="onClickHandler"
                />
            </div>
        </div>
        <!-- Modal -->
        <div
            class="fixed inset-0 bg-opacity-60 bg-black flex justify-center items-center"
            v-if="clicked"
        >
            <!-- Modal Body -->
            <div
                id="modal"
                class="bg-white rounded-[10px] overflow-x-auto flex flex-col justify-between md:w-2/6 w-3/4 h-3/4"
            >
                <!-- Modal Header -->
                <div
                    class="bg-gray-500 px-4 py-3 text-center text-white font-semibold text-[24px]"
                    v-if="updateOrCreate == 'update'"
                >
                    Update
                </div>
                <div
                    class="bg-gray-500 px-4 py-3 text-center text-white font-semibold text-[24px]"
                    v-if="updateOrCreate == 'Create'"
                >
                    Create
                </div>
                <!-- Modal Body -->
                <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
                    <label>Arriving arabic name</label>
                    <input
                        type="text"
                        class="w-full bg-gray-100 p-2 mt-2 mb-3"
                        v-model="arrivingArabicName"
                    />
                    <label>Arriving english name</label>
                    <input
                        type="text"
                        class="w-full bg-gray-100 p-2 mt-2 mb-3"
                        v-model="arrivingEnglishName"
                    />
                    <label>Sort</label>
                    <input type="text" class="w-full bg-gray-100 p-2 mt-2 mb-3" v-model="sort" />
                </div>
                <!-- Modal Footer -->
                <div class="bg-gray-200 px-4 py-3 text-right">
                    <button
                        type="button"
                        class="py-2 px-4 bg-gray-500 text-white rounded hover:bg-gray-700 mr-2"
                        @click="openModal()"
                    >
                        Cancel
                    </button>
                    <button
                        v-if="updateOrCreate == 'Create'"
                        type="button"
                        class="py-2 px-4 bg-blue-500 text-white rounded hover:bg-blue-700 mr-2"
                        @click="AddNewArriving()"
                    >
                        Create
                    </button>
                    <button
                        v-if="updateOrCreate == 'update'"
                        type="button"
                        class="py-2 px-4 bg-green-500 text-white rounded hover:bg-green-600 mr-2"
                        @click="UpdateArriving()"
                    >
                        Update
                    </button>
                </div>
            </div>
            <!-- Modal Footer -->
        </div>
    </div>
</template>

<script>
import axios from "axios";
export default {
    data() {
        return {
            recieveData: {},
            currentPage: 1,
            totalItems: "",
            rows: 10,
            id: null,
            arrivingArabicName: null,
            arrivingEnglishName: null,
            sort: null,
            clicked: false,
            updateOrCreate: "",
        };
    },
    mounted() {
        this.getArriving();
    },
    methods: {
        async getArriving(en) {
            let param = {};
            this.currentPage != ""
                ? (param = { page: en, first: 0, rows: this.rows })
                : (param = {
                      first: 0,
                      page: 0,
                      rows: this.rows,
                  });
            const response = await axios.get(
                "http://40.127.194.127:777/api/Emergency/GetAllArrivingMethods",
                {
                    params: param,
                }
            );
            this.totalItems = response.data.totalCount;
            this.recieveData = response.data.data;
        },
        async UpdateArriving() {
            const apiUrl = "http://40.127.194.127:777/api/Emergency/AddOrUpdateArrivingMethod";
            const response = await axios.post(
                apiUrl,
                {
                    id: this.id,
                    accountId: "1",
                    arrivingArabicName: this.arrivingArabicName,
                    arrivingEnglishName: this.arrivingEnglishName,
                    sort: this.sort,
                },
                {
                    headers: {
                        Authorization: `Bearer eyJhbGciOiJodHRwOi8vd3d3LnczLm9yZy8yMDAxLzA0L3htbGRzaWctbW9yZSNobWFjLXNoYTI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1lIjoic3VwZXJhZG1pbiIsImh0dHA6Ly9zY2hlbWFzLnhtbHNvYXAub3JnL3dzLzIwMDUvMDUvaWRlbnRpdHkvY2xhaW1zL3NpZCI6IjAwMDdjYWY1LWFjNjktNDViNi1hOTA1LWI1NjBlNTkxOWI4OSIsImV4cCI6MTY2ODA3MDA5MywiaXNzIjoiQ2xpbml2aXNvciIsImF1ZCI6IkNsaW5pdmlzb3IifQ.j0-PHpjpHmi63yxAtZRqbKaKXnMyPYoA7xhPO1P8kak')}`,
                        "Content-Type": "application/json",
                    },
                }
            );
            this.id = null;
            this.arrivingArabicName = null;
            this.arrivingEnglishName = null;
            this.sort = null;
            this.clicked = !this.clicked;
            this.getArriving();
        },
        async AddNewArriving() {
            const apiUrl = "http://40.127.194.127:777/api/Emergency/AddOrUpdateArrivingMethod";
            const response = await axios.post(
                apiUrl,
                {
                    accountId: "1",
                    arrivingArabicName: this.arrivingArabicName,
                    arrivingEnglishName: this.arrivingEnglishName,
                    sort: this.sort,
                },
                {
                    headers: {
                        Authorization: `Bearer eyJhbGciOiJodHRwOi8vd3d3LnczLm9yZy8yMDAxLzA0L3htbGRzaWctbW9yZSNobWFjLXNoYTI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1lIjoic3VwZXJhZG1pbiIsImh0dHA6Ly9zY2hlbWFzLnhtbHNvYXAub3JnL3dzLzIwMDUvMDUvaWRlbnRpdHkvY2xhaW1zL3NpZCI6IjAwMDdjYWY1LWFjNjktNDViNi1hOTA1LWI1NjBlNTkxOWI4OSIsImV4cCI6MTY2ODA3MDA5MywiaXNzIjoiQ2xpbml2aXNvciIsImF1ZCI6IkNsaW5pdmlzb3IifQ.j0-PHpjpHmi63yxAtZRqbKaKXnMyPYoA7xhPO1P8kak')}`,
                        "Content-Type": "application/json",
                    },
                }
            );
            this.id = null;
            this.arrivingArabicName = null;
            this.arrivingEnglishName = null;
            this.sort = null;
            this.clicked = !this.clicked;
            this.getArriving();
        },
        async deleteArriving(id) {
            const uid = JSON.stringify(id);
            const apiUrl = "http://40.127.194.127:777/api/Emergency/DeleteArrivingMethod";
            if (!confirm("Are you sure to remove it?")) return;
            const response = await axios.post(apiUrl, uid, {
                headers: {
                    Authorization: `Bearer eyJhbGciOiJodHRwOi8vd3d3LnczLm9yZy8yMDAxLzA0L3htbGRzaWctbW9yZSNobWFjLXNoYTI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1lIjoic3VwZXJhZG1pbiIsImh0dHA6Ly9zY2hlbWFzLnhtbHNvYXAub3JnL3dzLzIwMDUvMDUvaWRlbnRpdHkvY2xhaW1zL3NpZCI6IjAwMDdjYWY1LWFjNjktNDViNi1hOTA1LWI1NjBlNTkxOWI4OSIsImV4cCI6MTY2ODA3MDA5MywiaXNzIjoiQ2xpbml2aXNvciIsImF1ZCI6IkNsaW5pdmlzb3IifQ.j0-PHpjpHmi63yxAtZRqbKaKXnMyPYoA7xhPO1P8kak')}`,
                    "Content-Type": "application/json",
                },
            });
            this.getArriving();
        },
        openModal() {
            this.clicked = !this.clicked;
            this.id = null;
            this.arrivingArabicName = null;
            this.arrivingEnglishName = null;
            this.sort = null;
        },
        fillModal(el) {
            this.clicked = !this.clicked;
            this.arrivingArabicName = el.arrivingArabicName;
            this.arrivingEnglishName = el.arrivingEnglishName;
            this.sort = el.sort;
            this.id = el.id;
        },
        onClickHandler(en) {
            this.getArriving(en - 1);
            window.scrollTo(0, 0);
        },
    },
};
</script>

<style>
.pagination-container {
    display: flex;
    column-gap: 10px;
}
.paginate-buttons {
    height: 40px;
    width: 40px;
    border-radius: 20px;
    cursor: pointer;
    background-color: rgb(242, 242, 242);
    border: 1px solid rgb(217, 217, 217);
    color: black;
}
.paginate-buttons:hover {
    background-color: #d8d8d8;
}
.active-page {
    background-color: #3498db;
    border: 1px solid #3498db;
    color: white;
}
.active-page:hover {
    background-color: #2988c8;
}
</style>
