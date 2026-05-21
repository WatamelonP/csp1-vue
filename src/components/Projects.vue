<template>
  <!-- PROJECTS -->
  <section id="projects">
    <div class="container">
      <div class="section-title">PROJECTS</div>

      <div class="projects-grid pt-3">
        <ProjectsCard
          v-for="(project, index) in projects"
          :key="project.id"
          :project="project"
          :isActive="selectedIndex === index"
          @select="selectedIndex = index"
        />
      </div>

      <!-- Meta panel for selected project -->
      <div class="project-meta-wrapper" v-if="selectedProject">
        <div class="project-meta" style="display: grid;">
          <div>
            <div class="meta-label">Project Name</div>
            <div class="meta-title">{{ selectedProject.name }}</div>
            <div class="meta-label">Description</div>
            <p class="meta-desc">{{ selectedProject.description }}</p>
          </div>
          <div>
            <div class="meta-label">Date of Deployment</div>
            <div class="meta-date">{{ selectedProject.date }}</div>
            <div class="meta-url-row">
              <span class="meta-url">{{ selectedProject.url }}</span>
              <button class="btn-sm-outline" :disabled="!selectedProject.visitEnabled">Visit Website</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, computed } from 'vue'
import ProjectsCard from './ProjectsCard.vue'
import projects from '../data/projects.json'

const selectedIndex = ref(0)
const selectedProject = computed(() => projects[selectedIndex.value])
</script>
