
<template>
    <div class="container-fluid">
        <div class="row my-5">
            <h1 class="text-center text-primary mb-5">Why choose Zuitt?</h1>
            <div id="highlight1" class="col-md-4">
                <div id="highlight1" class="highlight-card card p-2 min-height-100 shadow-lg">
                    <div class="card-body">
                        <h1 class="text-center display-1 text-primary"><i class="bi bi-house-heart"></i></h1>
                        <h4 class="card-title text-center">
                            Learn from Home
                        </h4>
                        <p class="card-text mt-3">
                            Access world-class courses from the comfort of your own space, giving you the opportunity to learn new skills, practice hands-on exercises, and gain practical knowledge at your own pace. All you need is a device and an internet connection to explore structured lessons, guided projects, and interactive content designed to help you grow and achieve your learning goals.
                        </p>
                    </div>
                </div>
            </div>

            <div id="highlight2" class="col-md-4">
                <div class="highlight-card card p-2 min-height-100 shadow-lg">
                    <div class="card-body">
                        <h1 class="text-center display-1 text-primary"><i class="bi bi-cash-coin"></i></h1>
                        <h4 class="card-title text-center">
                            Study Now, Pay Later
                        </h4>
                        <p class="card-text mt-3">
                            Don't let finances hold you back from reaching your learning goals. Enroll today and take advantage of flexible payment options that fit your budget, allowing you to focus on mastering new skills. With a schedule that works for you, invest in your education at your own pace, gain access to valuable resources, and build the knowledge and confidence needed to succeed.
                        </p>
                    </div>
                </div>
            </div>

            <div id="highlight3" class="col-md-4">
                <div class="highlight-card card p-2 min-height-100 shadow-lg">
                    <div class="card-body">
                        <h1 class="text-center display-1 text-primary"><i class="bi bi-people"></i></h1>
                        <h4 class="card-title text-center">
                            Be Part of Our Community
                        </h4>
                        <p class="card-text mt-3">
                            Join thousands of learners from around the world, collaborate with peers on projects and discussions, and expand your professional network while developing practical, real-world skills. Through interactive lessons, hands-on exercises, and community engagement, you can gain valuable experience, exchange ideas, and build connections that support your growth and success.
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.highlights-section {
    background: var(--surface-soft);
    padding: 2rem 1rem;
}

.highlight-icon-wrap {
    width: 64px;
    height: 64px;
    border-radius: 50%;
    background: #eaf1ff;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 auto;
    font-size: 1.6rem;
    color: var(--primary);
}

.highlight-card {
    transition: transform 0.15s ease, box-shadow 0.15s ease;
}

.highlight-card:hover {
    transform: translateY(-3px);
    box-shadow: 0 14px 28px rgba(15, 23, 42, 0.1);
}
</style>