<script>
    import { getAuth, createUserWithEmailAndPassword } from "firebase/auth";
    import app from "../../app.js";
    import { goto } from "$app/navigation";
    import { doc, setDoc, getDoc, getFirestore, deleteDoc, addDoc, collection } from "firebase/firestore";

    let email = "";
    let password = "";
    let grade = "";
    let birthdate;
    let section = "";
    let gender = "male";
    let firstName = "";
    let lastName = "";
    let isAdmin = false;

    $: fullName = `${firstName} ${lastName}`;

    const createAccount = async () => {
        document.getElementById("form-error").innerHTML = "";
        const auth = getAuth(app);
        const db = getFirestore(app);
        const info = {
            email,
            password,
            grade,
            birthdate,
            section,
            gender,
            fullName,
            isAdmin
        };

        try {
            const docRef = doc(db, "users", fullName.toLowerCase());
            const docSnap = await getDoc(docRef);

            if (docSnap.exists()) {
                throw new Error("Student already exists.");
            }

            await setDoc(doc(db, "users", fullName.toLowerCase()), info);
            const userCredential = await createUserWithEmailAndPassword(
                auth,
                email,
                password
            );
            localStorage.setItem("uid", userCredential.user.uid);
            localStorage.setItem("emailAddress", email);
            goto("/");
        } catch (error) {
            document.getElementById("form-error").style.color = "red";
            document.getElementById("form-error").innerHTML = error.message;

            await deleteDoc(doc(db, "users", fullName.toLowerCase()))
        }
    };

    const checkIfSameWithPassword = (e) => {
        const currentPassword = e.target.value;
        document.getElementById("error_message").innerHTML = "";

        if (password !== currentPassword) {
            document.getElementById("error_message").style.color = "red";
            document.getElementById("error_message").innerHTML =
                "Passwords don't match";
        }
    };
</script>

<div class="register flex h-screen">
    <div
        class="w-4/12 bg-white rounded-lg shadow dark:border-gray-700 m-auto p-5"
    >
        <div class="p-5">
            <h5
                class="mb-2 text-2xl font-bold tracking-tight text-blue-500 text-center"
            >
                OHARA
            </h5>
            <div class="mt-10">
                <h5 class="font-semibold text-blue-500">Register</h5>
                <p class="text-gray-400 font-extralight mt-2 text-sm">
                    Create an account with our digital library, OHARA!
                </p>
            </div>

            <div id="form-error" />

            <form on:submit|preventDefault={createAccount} class="mt-5">
                <div class="relative z-0 w-full mb-6 group">
                    <input
                        type="email"
                        name="floating_email"
                        id="floating_email"
                        class="block py-2.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                        placeholder=" "
                        required
                        bind:value={email}
                    />
                    <label
                        for="floating_email"
                        class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                        >E-mail address</label
                    >
                </div>
                <div class="relative z-0 w-full mb-6 group">
                    <input
                        type="password"
                        name="floating_password"
                        id="floating_password"
                        class="block py-2.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                        placeholder=" "
                        required
                        bind:value={password}
                    />
                    <label
                        for="floating_password"
                        class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                        >Password</label
                    >
                </div>
                <div class="relative z-0 w-full mb-6 group">
                    <input
                        type="password"
                        name="repeat_password"
                        id="floating_repeat_password"
                        class="block py-2.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                        placeholder=" "
                        required
                        on:change={checkIfSameWithPassword}
                    />

                    <label
                        for="floating_repeat_password"
                        class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                        >Confirm password</label
                    >
                    <span id="error_message" class="error" />
                </div>
                <div class="grid md:grid-cols-2 md:gap-6">
                    <div class="relative z-0 w-full mb-6 group">
                        <input
                            type="text"
                            name="floating_first_name"
                            id="floating_first_name"
                            class="block py-2.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                            placeholder=" "
                            required
                            bind:value={firstName}
                        />
                        <label
                            for="floating_first_name"
                            class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                            >First name</label
                        >
                    </div>
                    <div class="relative z-0 w-full mb-6 group">
                        <input
                            type="text"
                            name="floating_last_name"
                            id="floating_last_name"
                            class="block py-2.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                            placeholder=" "
                            required
                            bind:value={lastName}
                        />
                        <label
                            for="floating_last_name"
                            class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                            >Last name</label
                        >
                    </div>
                </div>
                <div class="grid md:grid-cols-2 md:gap-6">
                    <div class="relative z-0 w-full mb-6 group">
                        <input
                            type="text"
                            name="floating_grade"
                            id="floating_grade"
                            class="block py-2.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                            placeholder=" "
                            required
                            bind:value={grade}
                        />
                        <label
                            for="floating_grade"
                            class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                            >Grade</label
                        >
                    </div>
                    <div class="relative z-0 w-full mb-6 group">
                        <input
                            type="text"
                            name="floating_section"
                            id="floating_section"
                            class="block py-2.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                            placeholder=" "
                            required
                            bind:value={section}
                        />
                        <label
                            for="floating_section"
                            class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                            >Section</label
                        >
                    </div>
                </div>
                <div class="grid md:grid-cols-2 md:gap-6">
                    <div class="relative z-0 w-full mb-6 group">
                        <label for="underline_gender" class="sr-only"
                            >Choose a gender</label
                        >
                        <select
                            id="underline_gender"
                            class="block py-2.5 px-0 w-full text-sm text-gray-500 bg-transparent border-0 border-b-2 border-gray-200 appearance-none focus:outline-none focus:ring-0 focus:border-gray-200 peer"
                            bind:value={gender}
                        >
                            <option selected value="male">Male</option>
                            <option value="female">Female</option>
                        </select>
                    </div>
                    <div class="relative z-0 w-full mb-6 group">
                        <input
                            type="date"
                            name="floating_birthdate"
                            id="floating_birthdate"
                            class="block py-2.5 px-0 w-full text-sm text-gray-500 bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                            placeholder=" "
                            required
                            bind:value={birthdate}
                        />
                        <label
                            for="floating_birthdate"
                            class="peer-focus:font-medium absolute text-sm text-gray-500 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                            >Birthdate</label
                        >
                    </div>
                </div>

                <button
                    type="submit"
                    class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm w-full px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
                    >Register</button
                >
            </form>

            <p class="text-blue-500 mt-5 font-lighter text-sm">
                Already have an account? <a
                    href="/login"
                    class="font-semibold hover:underline">Click here</a
                >
            </p>
        </div>
    </div>
</div>

<style>
    .register {
        height: 100vh;
    }

    .error {
        font-weight: lighter;
        font-size: smaller;
    }
</style>
