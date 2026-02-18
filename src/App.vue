<!-- Script Js ---------------------------------------------------------- -->
<script setup>
import { ref } from 'vue'
import html2pdf from 'html2pdf.js'

// Identification
const firstName = ref('')
const lastName = ref('')
const jobTitle = ref('')

// Contact Information
const contactNumber = ref('')
const contactEmail = ref('')
const contactLocalisation = ref('')
const contactSocialMedia = ref('')

// Education
const educationDegree = ref('')
const educationInstitution = ref('')
const educationGraduationYear = ref('')
const educationList = ref([])

// Skills
const skills = ref('')
const skillsList = ref([])

// About Me
const aboutMe = ref('')

// Experience
const experiencePlace = ref('')
const experience = ref('')
const experienceYear = ref('')
const experienceJobTitle = ref('')
const experienceList = ref([])

// Profile Image
const profileImage = ref(null)

// Image upload handler
const handleImageUpload = (event) => {
  const file = event.target.files[0]
  if (file) {
    const reader = new FileReader()
    reader.onload = (e) => {
      profileImage.value = e.target.result
    }
    reader.readAsDataURL(file)
  }
}

// Add education
const addEducation = () => {
  if(educationDegree.value.trim() && educationInstitution.value.trim() && educationGraduationYear.value.trim()) {
    educationList.value.push({
      degree: educationDegree.value,
      institution: educationInstitution.value,
      graduationYear: educationGraduationYear.value
    })
    educationDegree.value = ''
    educationInstitution.value = ''
    educationGraduationYear.value = ''
  } else {
    alert('Veuillez remplir tous les champs')
  }
}

// Add skill
const addSkill = () => {
  if(skills.value.trim()) {
    skillsList.value.push(skills.value)
    skills.value = ''
  } else {
    alert('Veuillez entrer une compétence')
  }
}

// Add experience
const addExperience = () => {
  if(experiencePlace.value.trim() && experience.value.trim() && experienceYear.value.trim() && experienceJobTitle.value.trim()) {
    experienceList.value.push({
      place: experiencePlace.value,
      experience: experience.value,
      experienceYear: experienceYear.value,
      jobTitle: experienceJobTitle.value
    })
    experiencePlace.value = ''
    experience.value = ''
    experienceYear.value = ''
    experienceJobTitle.value = ''
  } else {
    alert('Veuillez remplir tous les champs')
  }
}

// PDF Download
const cvContent = ref(null)

const downloadPDFAlternative = () => {
  const element = cvContent.value
  
  // Clone l'élément pour éviter les modifications pendant la capture
  const cloneElement = element.cloneNode(true)
  cloneElement.style.height = 'auto'
  cloneElement.style.overflow = 'visible'
  cloneElement.style.maxHeight = 'none'
  
  // Crée un conteneur temporaire
  const tempContainer = document.createElement('div')
  tempContainer.appendChild(cloneElement)
  tempContainer.style.position = 'absolute'
  tempContainer.style.left = '-9999px'
  tempContainer.style.top = '0'
  tempContainer.style.width = '210mm' // Largeur A4
  document.body.appendChild(tempContainer)
  
  const opt = {
    margin: [0.5, 0.5, 0.5, 0.5],
    filename: `${firstName.value || 'mon'}-${lastName.value || 'cv'}.pdf`,
    image: { type: 'jpeg', quality: 0.98 },
    html2canvas: { 
      scale: 2,
      scrollY: 0,
      windowHeight: cloneElement.scrollHeight,
      logging: true
    },
    jsPDF: { 
      unit: 'in', 
      format: 'a4', 
      orientation: 'portrait'
    }
  }
  
  html2pdf().from(cloneElement).set(opt).save().then(() => {
    // Nettoie l'élément temporaire
    document.body.removeChild(tempContainer)
  })
}

</script>

<!-- Template  ---------------------------------------------------------- -->
<template>
  <div class="cv-generator">
    <div class="form-section">
      <form @submit.prevent class="form-container">
        <h1>Générateur de CV</h1>
        
        <div class="form-group">
          <h2>Identification</h2>
          <div class="input-group">
            <input type="text" placeholder="Prénom" v-model="firstName" class="form-input">
            <input type="text" placeholder="Nom" v-model="lastName" class="form-input">
            <input type="text" placeholder="Titre professionnel" v-model="jobTitle" class="form-input">
          </div>
        </div>

        <div class="form-group">
          <h2>Informations de contact</h2>
          <div class="input-group">
            <input type="tel" placeholder="Téléphone" v-model="contactNumber" class="form-input">
            <input type="email" placeholder="Email" v-model="contactEmail" class="form-input">
            <input type="text" placeholder="Localisation" v-model="contactLocalisation" class="form-input">
            <input type="text" placeholder="Réseaux sociaux" v-model="contactSocialMedia" class="form-input">
          </div>
        </div>

        <div class="form-group">
          <h2>Formation</h2>
          <div class="input-group">
            <input type="text" placeholder="Diplôme" v-model="educationDegree" class="form-input">
            <input type="text" placeholder="Établissement" v-model="educationInstitution" class="form-input">
            <input type="text" placeholder="Année d'obtention" v-model="educationGraduationYear" class="form-input">
            <button @click="addEducation" class="btn-add">Ajouter</button>
          </div>
        </div>

        <div class="form-group">
          <h2>Compétences</h2>
          <div class="input-group">
            <input type="text" placeholder="Compétence" v-model="skills" class="form-input">
            <button @click="addSkill" class="btn-add">Ajouter</button>
          </div>
        </div>

        <div class="form-group">
          <h2>À propos</h2>
          <div class="input-group">
            <textarea placeholder="Parlez de vous..." v-model="aboutMe" class="form-textarea" rows="4"></textarea>
          </div>
        </div>

        <div class="form-group">
          <h2>Expérience professionnelle</h2>
          <div class="input-group">
            <input type="text" placeholder="Entreprise" v-model="experiencePlace" class="form-input">
            <input type="text" placeholder="Poste" v-model="experienceJobTitle" class="form-input">
            <input type="text" placeholder="Description" v-model="experience" class="form-input">
            <input type="text" placeholder="Année" v-model="experienceYear" class="form-input">
            <button @click="addExperience" class="btn-add">Ajouter</button>
          </div>
        </div>
      </form>
    </div>

    <div class="cv-preview" ref="cvContent">
      <div class="cv-header">
        <div class="profile-image-container">
          <img v-if="profileImage" :src="profileImage" alt="Photo de profil" class="profile-image">
          <div v-else class="profile-image-placeholder">
            <span>
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-camera-icon lucide-camera"><path d="M13.997 4a2 2 0 0 1 1.76 1.05l.486.9A2 2 0 0 0 18.003 7H20a2 2 0 0 1 2 2v9a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V9a2 2 0 0 1 2-2h1.997a2 2 0 0 0 1.759-1.048l.489-.904A2 2 0 0 1 10.004 4z"/><circle cx="12" cy="13" r="3"/></svg>
            </span>
          </div>
          <input type="file" @change="handleImageUpload" accept="image/*" class="image-upload-input" id="imageUpload">
          <label for="imageUpload" class="image-upload-label">Changer la photo</label>
        </div>
        <div class="profile-info">
          <h1 class="profile-name">{{ firstName || 'Prénom' }} {{ lastName || 'Nom' }}</h1>
          <h2 class="profile-title">{{ jobTitle || 'Titre professionnel' }}</h2>
        </div>
      </div>

      <div class="cv-body">
        <div class="cv-sidebar">
          <div class="sidebar-section">
            <h3 class="section-title">CONTACT</h3>
            <ul class="contact-list">
              <li v-if="contactNumber">
                <span class="contact-icon">
                  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-smartphone-icon lucide-smartphone"><rect width="14" height="20" x="5" y="2" rx="2" ry="2"/><path d="M12 18h.01"/></svg>
              </span> {{ contactNumber }}
            </li>
              <li v-if="contactEmail">
                <span class="contact-icon">
                  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-mail-icon lucide-mail"><path d="m22 7-8.991 5.727a2 2 0 0 1-2.009 0L2 7"/><rect x="2" y="4" width="20" height="16" rx="2"/></svg>
                </span> {{ contactEmail }}
              </li>
              <li v-if="contactLocalisation">
                <span class="contact-icon">
                  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-locate-icon lucide-locate"><line x1="2" x2="5" y1="12" y2="12"/><line x1="19" x2="22" y1="12" y2="12"/><line x1="12" x2="12" y1="2" y2="5"/><line x1="12" x2="12" y1="19" y2="22"/><circle cx="12" cy="12" r="7"/></svg>
                </span> {{ contactLocalisation }}
              </li>
              <li v-if="contactSocialMedia">
                <span class="contact-icon">
                  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-link-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"/><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"/></svg>
                </span> {{ contactSocialMedia }}
              </li>
            </ul>
          </div>

          <div class="sidebar-section">
            <h3 class="section-title">FORMATION</h3>
            <ul class="education-list">
              <li v-if="educationDegree" class="education-item">
                <h4>{{ educationInstitution }}</h4>
                <p class="education-detail">{{ educationDegree }} - {{ educationGraduationYear }}</p>
              </li>
              <li v-for="edu in educationList" :key="edu.degree" class="education-item">
                <h4>{{ edu.institution }}</h4>
                <p class="education-detail">{{ edu.degree }} - {{ edu.graduationYear }}</p>
              </li>
            </ul>
          </div>

          <div class="sidebar-section">
            <h3 class="section-title">COMPÉTENCES</h3>
            <ul class="skills-list">
              <li v-if="skills" class="skill-item">{{ skills }}</li>
              <li v-for="skill in skillsList" :key="skill" class="skill-item">{{ skill }}</li>
            </ul>
          </div>
        </div>

        <div class="cv-main">
          <div v-if="aboutMe" class="main-section">
            <h3 class="section-title">À PROPOS</h3>
            <p class="about-text">{{ aboutMe }}</p>
          </div>

          <div class="main-section">
            <h3 class="section-title">EXPÉRIENCE PROFESSIONNELLE</h3>
            <ul class="experience-list">
              <li v-if="experiencePlace" class="experience-item">
                <h4>{{ experiencePlace }} | {{ experienceYear }}</h4>
                <h5>{{ experienceJobTitle }}</h5>
                <p>{{ experience }}</p>
              </li>
              <li v-for="exp in experienceList" :key="exp.place" class="experience-item">
                <h4>{{ exp.place }} | {{ exp.experienceYear }}</h4>
                <h5>{{ exp.jobTitle }}</h5>
                <p>{{ exp.experience }}</p>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>

    <div class="cv-actions">
      <button @click="downloadPDFAlternative" class="btn-download">
        <span class="btn-icon">
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-file-down-icon lucide-file-down"><path d="M6 22a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h8a2.4 2.4 0 0 1 1.704.706l3.588 3.588A2.4 2.4 0 0 1 20 8v12a2 2 0 0 1-2 2z"/><path d="M14 2v5a1 1 0 0 0 1 1h5"/><path d="M12 18v-6"/><path d="m9 15 3 3 3-3"/></svg>
        </span>
        Télécharger en PDF
      </button>
    </div>
  </div>
</template>

<!-- Style css ---------------------------------------------------------- -->
<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.cv-generator {
  display: grid;
  grid-template-columns: 1fr 1.2fr;
  gap: 2rem;
  max-width: 1400px;
  margin: 0 auto;
  padding: 2rem;
  background: #f8fafc;
  min-height: 100vh;
}

/* Formulaire */
.form-section {
  background: white;
  border-radius: 16px;
  padding: 2rem;
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
  max-height: calc(100vh - 4rem);
  overflow-y: auto;
}

.form-container {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.form-container h1 {
  font-size: 2rem;
  color: #1e293b;
  font-weight: 600;
  margin-bottom: 1rem;
}

.form-group h2 {
  font-size: 1.25rem;
  color: #475569;
  margin-bottom: 1rem;
  font-weight: 500;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.form-input,
.form-textarea {
  padding: 0.75rem 1rem;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 0.95rem;
  transition: all 0.2s;
  background: #f8fafc;
}

.form-input:focus,
.form-textarea:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
  background: white;
}

.btn-add {
  padding: 0.75rem;
  background: #3b82f6;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 0.95rem;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.2s;
}

.btn-add:hover {
  background: #2563eb;
}

/* Aperçu CV */
.cv-preview {
  background: white;
  border-radius: 16px;
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  position: sticky;
  top: 2rem;
  max-height: calc(100vh - 4rem);
  overflow-y: auto;
}

.cv-header {
  display: flex;
  align-items: center;
  gap: 2rem;
  padding: 2rem;
  background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%);
  color: white;
}

.profile-image-container {
  position: relative;
  width: 120px;
  height: 120px;
}

.profile-image,
.profile-image-placeholder {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  object-fit: cover;
  border: 4px solid rgba(255, 255, 255, 0.2);
}

.profile-image-placeholder {
  background: rgba(255, 255, 255, 0.1);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2.5rem;
}

.image-upload-input {
  display: none;
}

.image-upload-label {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(255, 255, 255, 0.9);
  color: #1e293b;
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  font-size: 0.8rem;
  cursor: pointer;
  white-space: nowrap;
  opacity: 0;
  transition: opacity 0.2s;
}

.profile-image-container:hover .image-upload-label {
  opacity: 1;
}

.profile-info {
  flex: 1;
}

.profile-name {
  font-size: 2rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.profile-title {
  font-size: 1.25rem;
  font-weight: 400;
  opacity: 0.9;
}

/* Corps du CV */
.cv-body {
  display: grid;
  grid-template-columns: 300px 1fr;
  min-height: 600px;
}

.cv-sidebar {
  background: #f8fafc;
  padding: 2rem 1.5rem;
  border-right: 1px solid #e2e8f0;
}

.cv-main {
  padding: 2rem;
}

.section-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: #1e293b;
  margin-bottom: 1.25rem;
  padding-bottom: 0.5rem;
  border-bottom: 2px solid #3b82f6;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

/* Contact */
.contact-list {
  list-style: none;
}

.contact-list li {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin-bottom: 0.75rem;
  color: #475569;
  font-size: 0.95rem;
}

.contact-icon {
  font-size: 1.1rem;
  min-width: 24px;
}

/* Formation */
.education-list {
  list-style: none;
}

.education-item {
  margin-bottom: 1.25rem;
}

.education-item h4 {
  font-size: 1rem;
  color: #1e293b;
  font-weight: 600;
  margin-bottom: 0.25rem;
}

.education-detail {
  font-size: 0.9rem;
  color: #64748b;
}

/* Compétences */
.skills-list {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.skill-item {
  background: #e2e8f0;
  color: #1e293b;
  padding: 0.4rem 0.8rem;
  border-radius: 20px;
  font-size: 0.9rem;
  font-weight: 500;
}

/* Expérience */
.experience-list {
  list-style: none;
}

.experience-item {
  margin-bottom: 1.5rem;
  padding-bottom: 1.5rem;
  border-bottom: 1px solid #e2e8f0;
}

.experience-item:last-child {
  border-bottom: none;
  padding-bottom: 0;
}

.experience-item h4 {
  font-size: 1.1rem;
  color: #1e293b;
  font-weight: 600;
  margin-bottom: 0.25rem;
}

.experience-item h5 {
  font-size: 0.95rem;
  color: #3b82f6;
  font-weight: 500;
  margin-bottom: 0.5rem;
}

.experience-item p {
  font-size: 0.95rem;
  color: #475569;
  line-height: 1.5;
}

/* À propos */
.about-text {
  font-size: 0.95rem;
  color: #475569;
  line-height: 1.6;
}

/* Actions */
.cv-actions {
  position: fixed;
  bottom: 2rem;
  right: 2rem;
  z-index: 10;
}

.btn-download {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 1rem 2rem;
  background: #3b82f6;
  color: white;
  border: none;
  border-radius: 50px;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  box-shadow: 0 10px 25px -5px rgba(59, 130, 246, 0.5);
  transition: all 0.2s;
}

.btn-download:hover {
  background: #2563eb;
  transform: translateY(-2px);
  box-shadow: 0 15px 30px -5px rgba(59, 130, 246, 0.6);
}

.btn-icon {
  font-size: 1.2rem;
}

/* Responsive */
@media (max-width: 1024px) {
  .cv-generator {
    grid-template-columns: 1fr;
    gap: 1rem;
  }
  
  .form-section,
  .cv-preview {
    max-height: none;
    position: static;
  }
  
  .cv-actions {
    position: static;
    margin-top: 1rem;
    display: flex;
    justify-content: center;
  }
}

@media (max-width: 768px) {
  .cv-generator {
    padding: 1rem;
  }
  
  .cv-header {
    flex-direction: column;
    text-align: center;
    gap: 1rem;
  }
  
  .cv-body {
    grid-template-columns: 1fr;
  }
  
  .cv-sidebar {
    border-right: none;
    border-bottom: 1px solid #e2e8f0;
  }
  
  .profile-name {
    font-size: 1.5rem;
  }
  
  .profile-title {
    font-size: 1rem;
  }
}

/* Scroll personnalisé */
.form-section::-webkit-scrollbar,
.cv-preview::-webkit-scrollbar {
  width: 8px;
}

.form-section::-webkit-scrollbar-track,
.cv-preview::-webkit-scrollbar-track {
  background: #f1f5f9;
  border-radius: 4px;
}

.form-section::-webkit-scrollbar-thumb,
.cv-preview::-webkit-scrollbar-thumb {
  background: #94a3b8;
  border-radius: 4px;
}

.form-section::-webkit-scrollbar-thumb:hover,
.cv-preview::-webkit-scrollbar-thumb:hover {
  background: #64748b;
}
</style>