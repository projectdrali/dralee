<script>
    import {
        auth,
        GoogleAuthProvider,
        signInWithPopup,
        signOut,
        db,
        collection,
        addDoc,
        getDocs,
    } from "$lib/firebase";
    import { onAuthStateChanged } from "firebase/auth";
    import { writable } from "svelte/store";

    let user = writable(null);

    onAuthStateChanged(auth, (u) => user.set(u));

    async function login() {
        const provider = new GoogleAuthProvider();
        await signInWithPopup(auth, provider);
    }

    async function logout() {
        await signOut(auth);
    }

    async function saveData() {
        if (!auth.currentUser) return;
        await addDoc(collection(db, "users"), {
            uid: auth.currentUser.uid,
            timestamp: Date.now(),
        });
    }

    async function fetchData() {
        const querySnapshot = await getDocs(collection(db, "users"));
        querySnapshot.forEach((doc) => console.log(doc.data()));
    }
</script>

<div class="flex flex-col items-center justify-center h-screen bg-gray-100">
    <div class="bg-white shadow-lg rounded-lg p-6 text-center">
        {#if $user}
            <p class="text-xl font-semibold mb-4">
                Welcome, {$user.displayName}!
            </p>
            <img
                class="w-16 h-16 rounded-full mx-auto mb-4"
                src={$user.photoURL}
                alt=""
            />
            <button
                class="bg-red-500 text-white px-4 py-2 rounded-lg shadow"
                on:click={logout}>Logout</button
            >
            <div class="mt-4 space-x-2">
                <button
                    class="bg-blue-500 text-white px-4 py-2 rounded-lg shadow"
                    on:click={saveData}>Save Data</button
                >
                <button
                    class="bg-green-500 text-white px-4 py-2 rounded-lg shadow"
                    on:click={fetchData}>Fetch Data</button
                >
            </div>
        {:else}
            <p class="text-lg font-semibold mb-4">Sign in to continue</p>
            <button
                class="bg-blue-500 text-white px-4 py-2 rounded-lg shadow"
                on:click={login}>Login with Google</button
            >
        {/if}
    </div>
</div>
