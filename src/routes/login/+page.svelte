<script>
    import { getAuth, signInWithEmailAndPassword } from "firebase/auth";
    import app from "../../app.js";
    import { goto } from "$app/navigation";
    import {
        getDocs,
        getFirestore,
        collection,
        query,
        where,
    } from "firebase/firestore";

    let email;
    let password;
    let hasUser = false

    const loginAccount = async () => {
        const auth = getAuth(app);

        try {
            const db = getFirestore(app);
            const userRef = collection(db, "users");
            const q = query(userRef, where("email", "==", email));
            const querySnapshot = await getDocs(q);
            querySnapshot.forEach(data => {
                if (data.id) {
                    hasUser = true
                }
            })

            if (!hasUser) {
                throw new Error("Student not registered. Please create an account first.")
            }

            const userCredential = await signInWithEmailAndPassword(
                auth,
                email,
                password
            );
            localStorage.setItem("uid", userCredential.user.uid);
            localStorage.setItem("emailAddress", email);
            goto("/");
        } catch (error) {
            document.getElementById("login-error-message").style.color = "red";
            document.getElementById("login-error-message").innerHTML =
                error.message;
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
                <h5 class="font-semibold text-blue-500">Sign in</h5>
                <p class="text-gray-400 font-extralight mt-2 text-sm">
                    Please sign in your account.
                </p>
            </div>

            <div id="login-error-message" />

            <form on:submit|preventDefault={loginAccount} class="mt-5">
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

                <button
                    type="submit"
                    class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm w-full px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
                    >Sign In</button
                >
            </form>

            <p class="text-blue-500 mt-5 font-lighter text-sm">
                Don't have an account? <a
                    href="/register"
                    class="font-semibold hover:underline">Sign up here</a
                >
            </p>
        </div>
    </div>
</div>

<style>
    .register {
        height: 100vh;
    }
</style>
