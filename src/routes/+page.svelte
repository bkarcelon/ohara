<script>
    import SideNav from "../lib/components/SideNav.svelte";
    import { goto } from "$app/navigation";
    import { onMount } from "svelte";
    import logo from "../lib/images/img_avatar.png";
    import logo2 from "../lib/images/img_avatar2.png";
    import {
        getDocs,
        getFirestore,
        collection,
        query,
        where,
    } from "firebase/firestore";
    import app from "../app.js";

    let fullName = "";
    let grade = "";
    let section = "";
    let birthdate = "";
    let gender = "";

    onMount(async () => {
        const uid = localStorage.getItem("uid");

        if (!uid) {
            goto("/register");
            return;
        }

        const useremail = localStorage.getItem("emailAddress");
        const db = getFirestore(app);
        const q = query(
            collection(db, "users"),
            where("email", "==", useremail)
        );
        const querySnapshot = await getDocs(q);
        querySnapshot.forEach((doc) => {
            const data = doc.data();
            fullName = data.fullName;
            grade = data.grade;
            section = data.section;
            birthdate = data.birthdate;
            gender = data.gender;
            localStorage.setItem("isAdmin", data.isAdmin)
        });
    });
</script>

<SideNav />
<div class="index">
    <h1 class="title">Personal Information</h1>
    <hr />
    <div class="info">
        {#if gender === "male"}
            <img src={logo} alt="default" />
        {:else if gender === "female"}
            <img src={logo2} alt="default" />
        {/if}

        <p class="name">{fullName}</p>
        <div class="other-info">
            <div>
                <div class="relative details">
                    <p
                        id="floating_outlined_grade"
                        class="label block px-2.5 pb-2 pt-2 text-base text-gray-900 bg-transparent rounded-lg border-2 border-gray-500 appearance-none focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                    >
                        {grade}
                    </p>
                    <label
                        for="floating_outlined_grade"
                        class="absolute text-lg text-gray-500 transform -translate-y-5 scale-75 top-2 z-10 origin-[0] bg-white px-2 peer-focus:px-2 peer-focus:text-blue-600 peer-placeholder-shown:scale-100 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:top-1/2 peer-focus:top-2 peer-focus:scale-75 peer-focus:-translate-y-4 left-1"
                        >Grade</label
                    >
                </div>
                <div class="relative details">
                    <p
                        id="floating_outlined_grade"
                        class="label block px-2.5 pb-2 pt-2 w-full text-base text-gray-900 bg-transparent rounded-lg border-2 border-gray-500 appearance-none focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                    >
                        {section}
                    </p>
                    <label
                        for="floating_outlined_grade"
                        class="absolute text-lg text-gray-500 transform -translate-y-5 scale-75 top-2 z-10 origin-[0] bg-white px-2 peer-focus:px-2 peer-focus:text-blue-600 peer-placeholder-shown:scale-100 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:top-1/2 peer-focus:top-2 peer-focus:scale-75 peer-focus:-translate-y-4 left-1"
                        >Section</label
                    >
                </div>
            </div>

            <div>
                <div class="relative details">
                    <p
                        id="floating_outlined_grade"
                        class="label block px-2.5 pb-2 pt-2 text-base text-gray-900 bg-transparent rounded-lg border-2 border-gray-500 appearance-none focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                    >
                        {birthdate}
                    </p>
                    <label
                        for="floating_outlined_grade"
                        class="absolute text-lg text-gray-500 transform -translate-y-5 scale-75 top-2 z-10 origin-[0] bg-white px-2 peer-focus:px-2 peer-focus:text-blue-600 peer-placeholder-shown:scale-100 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:top-1/2 peer-focus:top-2 peer-focus:scale-75 peer-focus:-translate-y-4 left-1"
                        >Birthdate</label
                    >
                </div>
                <div class="relative details">
                    <p
                        id="floating_outlined_grade"
                        class="label block px-2.5 pb-2 pt-2 w-full text-base text-gray-900 bg-transparent rounded-lg border-2 border-gray-500 appearance-none focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                    >
                        {gender.toUpperCase()}
                    </p>
                    <label
                        for="floating_outlined_grade"
                        class="absolute text-lg text-gray-500 transform -translate-y-5 scale-75 top-2 z-10 origin-[0] bg-white px-2 peer-focus:px-2 peer-focus:text-blue-600 peer-placeholder-shown:scale-100 peer-placeholder-shown:-translate-y-1/2 peer-placeholder-shown:top-1/2 peer-focus:top-2 peer-focus:scale-75 peer-focus:-translate-y-4 left-1"
                        >Gender</label
                    >
                </div>
            </div>
        </div>
    </div>
</div>

<style>
    .index {
        padding: 20px 50px;
        background: white;
        border-radius: 10px;
        width: 70%;
        margin-top: 30px;
        margin-left: auto;
        margin-right: auto;
    }

    .title {
        padding: 0 0 20px 0;
        font-size: 30px;
        font-weight: 500;
    }

    .info {
        text-align: center;
    }

    img {
        border-radius: 50%;
        width: 150px;
        height: 150px;
        display: block;
        margin-left: auto;
        margin-right: auto;
        margin-top: 50px;
    }

    .name {
        margin-top: 20px;
        font-weight: bold;
        font-size: x-large;
    }

    .other-info {
        margin-top: 50px;
        margin-left: auto;
        margin-right: auto;
        text-align: left;
    }

    .label {
        width: 100%;
    }

    .details {
        margin: 20px;
    }
</style>
