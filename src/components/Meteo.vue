<template>
  <div class="meteo-app">
    <div class="container py-5">
      <div class="header-section mb-5">
        <h1 class="main-title text-center mb-4">🌍 MeteoNow</h1>
        <div class="search-section">
          <div class="form-group">
            <label for="position" class="form-label fw-bold">Entrez le nom d'une ville</label>
            <div class="search-wrapper">
              <input 
                id="position" 
                type="text" 
                class="form-control search-input" 
                v-model="requete" 
                v-on:keypress="goMeteo"
                placeholder="Cotonou, Paris, Londres, Tokyo..."
              >
              <button class="search-btn" @click="goMeteo({key: 'Enter'})" title="Rechercher">
                🔍
              </button>
            </div>
            <p v-if="erreur" class="error-message">{{ erreur }}</p>
            <p v-if="infoMessage" class="info-message">{{ infoMessage }}</p>
          </div>
        </div>
      </div>

      <div class="weather-result" v-if="temps">
        <div class="city-name-section mb-4">
          <h2 class="city-name text-center">{{ temps.name }}</h2>
          <p class="country-continent text-center">
            {{ pays }} 
            <span v-if="continent" class="continent-badge">{{ continent }}</span>
          </p>
        </div>
        
        <div class="weather-card">
          <div class="weather-grid">
            <div class="weather-item temperature-section">
              <p class="weather-label">Température</p>
              <p class="weather-value temp-value">{{ temps.main.temp }}°C</p>
            </div>
            
            <div class="weather-item description-section">
              <p class="weather-label">Conditions</p>
              <p class="weather-value desc-value">{{ temps.weather[0].description }}</p>
            </div>

            <div class="weather-item feels-section">
              <p class="weather-label">Ressenti</p>
              <p class="weather-value feels-value">{{ temps.main.feels_like }}°C</p>
            </div>

            <div class="weather-item humidity-section">
              <p class="weather-label">Humidité</p>
              <p class="weather-value humidity-value">{{ temps.main.humidity }}%</p>
            </div>
          </div>
        </div>
      </div>

      <footer class="footer-section">
        <p class="footer-text">&copy; 2026 MeteoNow. Réalisé par Jean Baptiste BOCO</p>
      </footer>
    </div>
  </div>
</template>

<script>
    
    import axios from 'axios'
 export default {
    name: 'Meteo',
    data(){
        return {
            requete: '',
            temps: undefined,
            pays: '',
            continent: '',
            erreur: '',
            infoMessage: '',
            api_code: 'c3279457c362c08819c21447b488af12',
            url_recherche: 'https://api.openweathermap.org/data/2.5/weather?',
            capitales: {
              'FR': 'Paris', 'DE': 'Berlin', 'IT': 'Rome', 'ES': 'Madrid', 'GB': 'Londres', 'PT': 'Lisbonne', 'BE': 'Bruxelles', 'NL': 'Amsterdam', 'AT': 'Vienne', 'CH': 'Berne', 'US': 'Washington', 'CA': 'Ottawa', 'MX': 'Mexico', 'BR': 'Brasília', 'JP': 'Tokyo', 'CN': 'Beijing', 'IN': 'New Delhi', 'RU': 'Moscou', 'BJ': 'Porto-Novo', 'NG': 'Abuja', 'ZA': 'Pretoria', 'EG': 'Le Caire', 'KE': 'Nairobi', 'ET': 'Addis-Abeba', 'SN': 'Dakar'
            },
            continents: {
              'AF': '🌍 Afrique',
              'AN': '🌎 Antarctique',
              'AS': '🌏 Asie',
              'EU': '🌍 Europe',
              'NA': '🌎 Amérique du Nord',
              'OC': '🌏 Océanie',
              'SA': '🌎 Amérique du Sud'
            },
            paysContinent: {
              'FR': 'EU', 'DE': 'EU', 'IT': 'EU', 'ES': 'EU', 'GB': 'EU', 'PT': 'EU', 'BE': 'EU', 'NL': 'EU', 'AT': 'EU', 'CH': 'EU', 'SE': 'EU', 'NO': 'EU', 'DK': 'EU', 'FI': 'EU', 'PL': 'EU', 'CZ': 'EU', 'SK': 'EU', 'HU': 'EU', 'RO': 'EU', 'BG': 'EU', 'GR': 'EU', 'HR': 'EU', 'SI': 'EU', 'LV': 'EU', 'LT': 'EU', 'EE': 'EU', 'IE': 'EU', 'CY': 'EU', 'MT': 'EU', 'LU': 'EU', 'UA': 'EU', 'BY': 'EU', 'MD': 'EU', 'RS': 'EU', 'BA': 'EU', 'ME': 'EU', 'MK': 'EU', 'AL': 'EU', 'IS': 'EU', 'TR': 'EU', 'KZ': 'AS', 'UZ': 'AS', 'TM': 'AS', 'KG': 'AS', 'TJ': 'AS', 'AF': 'AS', 'PK': 'AS', 'IN': 'AS', 'BD': 'AS', 'NP': 'AS', 'BT': 'AS', 'LK': 'AS', 'MM': 'AS', 'TH': 'AS', 'LA': 'AS', 'VN': 'AS', 'KH': 'AS', 'MY': 'AS', 'SG': 'AS', 'BN': 'AS', 'ID': 'AS', 'PH': 'AS', 'CN': 'AS', 'MN': 'AS', 'JP': 'AS', 'KR': 'AS', 'KP': 'AS', 'TW': 'AS', 'HK': 'AS', 'MO': 'AS', 'IR': 'AS', 'IQ': 'AS', 'SA': 'AS', 'AE': 'AS', 'QA': 'AS', 'BH': 'AS', 'KW': 'AS', 'OM': 'AS', 'YE': 'AS', 'JO': 'AS', 'LB': 'AS', 'SY': 'AS', 'IL': 'AS', 'PS': 'AS', 'EG': 'AF', 'LY': 'AF', 'TN': 'AF', 'DZ': 'AF', 'MA': 'AF', 'SD': 'AF', 'ET': 'AF', 'ER': 'AF', 'DJ': 'AF', 'SO': 'AF', 'KE': 'AF', 'UG': 'AF', 'TZ': 'AF', 'RW': 'AF', 'BI': 'AF', 'CD': 'AF', 'CG': 'AF', 'GA': 'AF', 'GQ': 'AF', 'CM': 'AF', 'CV': 'AF', 'BJ': 'AF', 'BF': 'AF', 'CI': 'AF', 'GM': 'AF', 'GH': 'AF', 'GN': 'AF', 'GW': 'AF', 'KM': 'AF', 'LR': 'AF', 'ML': 'AF', 'MZ': 'AF', 'NA': 'AF', 'NE': 'AF', 'NG': 'AF', 'SN': 'AF', 'SL': 'AF', 'ZA': 'AF', 'SS': 'AF', 'ZM': 'AF', 'ZW': 'AF', 'AO': 'AF', 'BW': 'AF', 'LS': 'AF', 'MW': 'AF', 'MG': 'AF', 'MU': 'AF', 'SC': 'AF', 'US': 'NA', 'CA': 'NA', 'MX': 'NA', 'GT': 'NA', 'BZ': 'NA', 'SV': 'NA', 'HN': 'NA', 'NI': 'NA', 'CR': 'NA', 'PA': 'NA', 'CU': 'NA', 'DO': 'NA', 'HT': 'NA', 'JM': 'NA', 'TT': 'NA', 'BB': 'NA', 'BS': 'NA', 'AG': 'NA', 'KN': 'NA', 'LC': 'NA', 'VC': 'NA', 'GD': 'NA', 'DM': 'NA', 'PR': 'NA', 'CO': 'SA', 'VE': 'SA', 'GY': 'SA', 'SR': 'SA', 'GF': 'SA', 'BR': 'SA', 'PE': 'SA', 'EC': 'SA', 'BO': 'SA', 'PY': 'SA', 'UY': 'SA', 'AR': 'SA', 'CL': 'SA', 'AU': 'OC', 'NZ': 'OC', 'FJ': 'OC', 'SB': 'OC', 'VU': 'OC', 'WS': 'OC', 'TO': 'OC', 'KI': 'OC', 'MH': 'OC', 'FM': 'OC', 'PW': 'OC', 'PG': 'OC', 'RU': 'EU'
            }
        }
      },
      methods:{
         goMeteo(e){
           if(e.key == "Enter" ){
            this.erreur = '';
            this.infoMessage = '';
            
            if(!this.requete.trim()){
              this.erreur = '⚠️ Veuillez entrer une ville ou un pays!';
              return;
            }

            axios
            .get(`${this.url_recherche}q=${this.requete}&units=metric&APPID=${this.api_code}&lang=fr`) 
            .then(reponse => {
              this.temps = reponse.data;
              const countryCode = reponse.data.sys.country;
              
              if(this.requete.length > 3 && this.capitales[countryCode] && 
                 this.requete.toLowerCase() !== reponse.data.name.toLowerCase()){
                this.infoMessage = `📍 Vous avez entré un pays! Affichage de sa capitale: ${this.capitales[countryCode]}`;
              }
              
              axios.get(`https://restcountries.com/v3.1/alpha/${countryCode}`)
                .then(countryResponse => {
                  const countryData = countryResponse.data[0];
                  this.pays = countryData.name.common;
                  const continentCode = this.paysContinent[countryCode] || 'AS';
                  this.continent = this.continents[continentCode];
                })
                .catch(err => {
                  console.log('Erreur récupération pays:', err);
                  this.continent = '';
                });
              
             console.log(this.temps);
            })
            .catch(err => {
              this.temps = undefined;
              this.erreur = '❌ Désolé, je n\'ai pas trouvé cette ville! Vérifiez l\'orthographe et réessayez.';
              console.log('Erreur:', err);
            });
            
            this.requete = '' 
           } 
         }
      }
   
}
 
</script>

<style scoped>
.meteo-app {
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  background-image: url('@/assets/img/Paysage.jpg');
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  position: relative;
  padding: 15px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.meteo-app::before {
  content: '';
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.8) 0%, rgba(118, 75, 162, 0.8) 100%);
  z-index: -1;
  pointer-events: none;
}

.container {
  max-width: 900px;
  margin: 0 auto;
  width: 100%;
  flex: 1;
  display: flex;
  flex-direction: column;
}

/* Header Section */
.header-section {
  text-align: center;
  margin-bottom: 2rem;
}

.main-title {
  font-size: clamp(1.5rem, 8vw, 3rem);
  font-weight: 700;
  color: white;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
  margin-bottom: 1.5rem;
  letter-spacing: 1px;
}

/* Search Section */
.search-section {
  max-width: 600px;
  margin: 0 auto;
  width: 100%;
}

.form-label {
  color: white;
  font-size: clamp(0.9rem, 4vw, 1.1rem);
  margin-bottom: 0.8rem;
  display: block;
}

.search-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  gap: 0;
}

.search-input {
  border: none;
  border-radius: 50px 0 0 50px;
  padding: clamp(10px, 3vw, 12px) clamp(15px, 4vw, 20px);
  font-size: clamp(0.9rem, 3.5vw, 1rem);
  width: 100%;
  box-sizing: border-box;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
  transition: all 0.3s ease;
}

.search-input:focus {
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
  outline: none;
  border: 2px solid #667eea;
}

.search-btn {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
  border-radius: 0 50px 50px 0;
  padding: clamp(10px, 3vw, 12px) clamp(12px, 3vw, 18px);
  font-size: clamp(1rem, 4vw, 1.3rem);
  cursor: pointer;
  transition: all 0.3s ease;
  color: white;
  font-weight: bold;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

.search-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
}

.search-btn:active {
  transform: scale(0.98);
}

.error-message {
  color: #ff6b6b;
  font-size: clamp(0.8rem, 3vw, 0.95rem);
  margin-top: 0.8rem;
  font-weight: 600;
  animation: shake 0.5s ease;
}

.info-message {
  color: #ffd93d;
  font-size: clamp(0.8rem, 3vw, 0.95rem);
  margin-top: 0.8rem;
  font-weight: 600;
  animation: fadeIn 0.5s ease;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* City Name */
.city-name-section {
  animation: slideDown 0.5s ease;
  margin-bottom: 1.5rem;
}

.city-name {
  font-size: clamp(1.5rem, 7vw, 2.5rem);
  font-weight: 700;
  color: white;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
  margin: 0;
}

.country-continent {
  font-size: clamp(0.9rem, 4vw, 1.1rem);
  color: rgba(255, 255, 255, 0.9);
  margin: 0.5rem 0 0 0;
  font-weight: 500;
}

.continent-badge {
  display: inline-block;
  background: rgba(255, 255, 255, 0.25);
  padding: 0.3rem 0.8rem;
  border-radius: 20px;
  margin-left: 0.5rem;
  font-weight: 600;
  font-size: clamp(0.8rem, 3vw, 1rem);
}

/* Weather Card */
.weather-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  padding: clamp(1rem, 5vw, 2rem);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
  animation: slideUp 0.5s ease, float 3s ease-in-out infinite;
  flex: 1;
}

.weather-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: clamp(1rem, 3vw, 1.5rem);
}

/* Weather Items */
.weather-item {
  padding: clamp(1rem, 4vw, 1.5rem);
  border-radius: 15px;
  text-align: center;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.weather-item:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.temperature-section {
  background: linear-gradient(135deg, #ff6b6b 0%, #ff8e53 100%);
}

.description-section {
  background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
}

.feels-section {
  background: linear-gradient(135deg, #fa709a 0%, #fee140 100%);
}

.humidity-section {
  background: linear-gradient(135deg, #30cfd0 0%, #330867 100%);
}

.weather-label {
  font-size: clamp(0.7rem, 2.5vw, 0.9rem);
  font-weight: 600;
  color: rgba(255, 255, 255, 0.9);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.5rem;
}

.weather-value {
  font-size: clamp(1.2rem, 5vw, 2rem);
  font-weight: 700;
  color: white;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
  margin: 0;
}

/* Animations */
@keyframes slideDown {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes float {
  0%, 100% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-15px);
  }
}

/* Footer */
.footer-section {
  margin-top: 2rem;
  text-align: center;
  padding-top: 1.5rem;
  border-top: 1px solid rgba(255, 255, 255, 0.3);
}

.footer-text {
  color: rgba(255, 255, 255, 0.8);
  font-size: clamp(0.8rem, 2.5vw, 0.95rem);
  margin: 0;
  font-weight: 500;
  transition: color 0.3s ease;
}

.footer-text:hover {
  color: white;
}

/* Mobile - Petit téléphone (320px - 480px) */
@media (max-width: 480px) {
  .meteo-app {
    padding: 10px;
    min-height: 100vh;
  }

  .header-section {
    margin-bottom: 1.5rem;
  }

  .main-title {
    margin-bottom: 1rem;
  }

  .search-input {
    font-size: 16px;
  }

  .weather-grid {
    grid-template-columns: 1fr;
  }

  .weather-item {
    padding: 1rem;
  }

  .footer-section {
    margin-top: 1.5rem;
    padding-top: 1rem;
  }
}

/* Tablette portrait (481px - 768px) */
@media (min-width: 481px) and (max-width: 768px) {
  .meteo-app {
    padding: 12px;
  }

  .container {
    max-width: 100%;
  }

  .weather-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .header-section {
    margin-bottom: 2rem;
  }

  .weather-card {
    padding: 1.5rem;
  }
}

/* Tablette landscape et petit écran (769px - 1024px) */
@media (min-width: 769px) and (max-width: 1024px) {
  .weather-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .container {
    max-width: 95%;
  }
}

/* Desktop (1025px et plus) */
@media (min-width: 1025px) {
  .container {
    max-width: 900px;
  }

  .weather-grid {
    grid-template-columns: repeat(4, 1fr);
  }

  .weather-item:hover {
    transform: translateY(-8px);
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
  }
}
</style>