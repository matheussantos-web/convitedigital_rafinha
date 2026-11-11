<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

// ==================== CONFIGURAÇÕES EDITÁVEIS ====================
const eventConfig = {
  title: 'Aniversário do Rafinha',
  dateLabel: '09/11/2026',
  timeLabel: '19 horas',
  location: 'Salão de Festas Aurora',
  address: 'Rua das Flores, 123 — Centro',
  dateTimeISO: '2026-11-09T19:00:00-03:00',
  mapsUrl: 'https://maps.google.com/?q=Rua%20das%20Flores%20123',
  audioSrc: '/audio/trihlha.mp3',
  backgroundSrc: '/images/rafael-theme.webp',
}

const rsvpOptions = [
  {
    value: 'yes',
    label: 'Vou',
    icon: '✅',
    idleClass: 'border-lime-300/70 bg-gradient-to-br from-green-500/85 to-emerald-700/85 text-white shadow-[0_0_18px_rgba(74,222,128,.38),inset_0_1px_0_rgba(255,255,255,.35)]',
    activeClass: 'border-yellow-300 bg-gradient-to-br from-lime-400 to-green-600 text-slate-950 shadow-[0_0_28px_rgba(163,230,53,.58),inset_0_1px_0_rgba(255,255,255,.5)] scale-[1.02]',
  },
  {
    value: 'maybe',
    label: 'Talvez',
    icon: '🤔',
    idleClass: 'border-yellow-300/70 bg-gradient-to-br from-amber-400/90 to-orange-600/90 text-slate-950 shadow-[0_0_18px_rgba(251,191,36,.34),inset_0_1px_0_rgba(255,255,255,.35)]',
    activeClass: 'border-yellow-200 bg-gradient-to-br from-yellow-300 to-orange-500 text-slate-950 shadow-[0_0_28px_rgba(251,191,36,.55),inset_0_1px_0_rgba(255,255,255,.55)] scale-[1.02]',
  },
  {
    value: 'no',
    label: 'Não vou',
    icon: '❌',
    idleClass: 'border-red-300/70 bg-gradient-to-br from-red-500/90 to-red-800/90 text-white shadow-[0_0_18px_rgba(248,113,113,.34),inset_0_1px_0_rgba(255,255,255,.30)]',
    activeClass: 'border-red-200 bg-gradient-to-br from-red-400 to-red-700 text-white shadow-[0_0_28px_rgba(239,68,68,.55),inset_0_1px_0_rgba(255,255,255,.45)] scale-[1.02]',
  },
]

const audioRef = ref(null)
const isMuted = ref(true)
const selectedRsvp = ref('')
const rsvpFeedback = ref('')
const countdown = ref({ days: 0, hours: 0, minutes: 0, seconds: 0 })
let timerId = null

const backgroundStyle = computed(() => ({
  backgroundImage: `url('${eventConfig.backgroundSrc}')`,
}))

const countdownParts = computed(() => [
  { label: 'Dias', value: countdown.value.days },
  { label: 'Horas', value: countdown.value.hours },
  { label: 'Minutos', value: countdown.value.minutes },
  { label: 'Segundos', value: countdown.value.seconds },
])

function updateCountdown() {
  const difference = new Date(eventConfig.dateTimeISO).getTime() - Date.now()

  if (difference <= 0) {
    countdown.value = { days: 0, hours: 0, minutes: 0, seconds: 0 }
    return
  }

  countdown.value = {
    days: Math.floor(difference / 86_400_000),
    hours: Math.floor((difference / 3_600_000) % 24),
    minutes: Math.floor((difference / 60_000) % 60),
    seconds: Math.floor((difference / 1_000) % 60),
  }
}

async function toggleAudio() {
  const music = document.getElementById('bg-music') || audioRef.value
  if (!music) return

  if (music.paused) {
    try {
      await music.play()
      isMuted.value = false
    } catch {
      // Navegadores podem bloquear áudio; nenhum erro textual é exibido ao convidado.
      isMuted.value = true
    }
  } else {
    music.pause()
    isMuted.value = true
  }
}

function selectRsvp(option) {
  selectedRsvp.value = option.value

  const messages = {
    yes: 'Que alegria! Sua presença está confirmada. 🎉',
    maybe: 'Sem problema! Você poderá confirmar novamente depois. 🤔',
    no: 'Sentiremos sua falta, mas agradecemos o aviso. 💜',
  }

  rsvpFeedback.value = messages[option.value]
}

onMounted(() => {
  updateCountdown()
  timerId = window.setInterval(updateCountdown, 1000)
})

onBeforeUnmount(() => {
  if (timerId) window.clearInterval(timerId)
})
</script>

<template>
  <main
    class="invite-page relative min-h-screen w-full overflow-hidden bg-cover bg-center bg-no-repeat text-white"
    :style="backgroundStyle"
  >
    <div class="absolute inset-0 pointer-events-none"></div>

    <audio id="bg-music" ref="audioRef" :src="eventConfig.audioSrc" loop preload="metadata"></audio>

    <button
      type="button"
      class="absolute right-4 top-4 z-20 grid h-12 w-12 place-items-center rounded-full border border-white/20 bg-black/40 text-xl shadow-lg backdrop-blur-md transition active:scale-95 sm:right-6 sm:top-6"
      :aria-label="isMuted ? 'Ativar trilha sonora' : 'Silenciar trilha sonora'"
      :title="isMuted ? 'Ativar som' : 'Silenciar som'"
      @click="toggleAudio"
    >
      <span aria-hidden="true">{{ isMuted ? '🔇' : '🔊' }}</span>
    </button>

    <section class="relative z-10 flex min-h-screen flex-col justify-between px-4 pb-6 pt-10 sm:px-6 sm:pt-14">
  <div class="mx-auto w-full max-w-lg sm:max-w-xl flex flex-col justify-between min-h-[90vh]">
    
  <!-- Cabeçalho com destaque máximo para a Missão de Aniversário -->
    <header class="mt-8 text-center sm:mt-12">
      <div class="inline-block rounded-full border border-cyan-400/40 bg-black/40 px-4 py-1 backdrop-blur-md shadow-[0_0_15px_rgba(34,211,238,0.3)]">
        <p class="text-[0.75rem] font-black uppercase tracking-[0.35em] text-cyan-300 drop-shadow-[0_2px_4px_rgba(0,0,0,1)]">
          Missão de Aniversário
        </p>
      </div>
      <h1 class="mt-2 text-balance text-3xl font-black leading-tight text-white drop-shadow-[0_4px_8px_rgba(0,0,0,1)] sm:text-5xl">
        {{ eventConfig.title }}
      </h1>
    </header>

    <!-- Bloco de Informações (Data, Horário, Local) posicionado mais abaixo -->
    <div class="mt-20 grid grid-cols-2 gap-2 text-sm sm:text-base">
      <div class="flex min-h-[4rem] items-center gap-2 rounded-xl border border-white/10 bg-black/20 px-2.5 backdrop-blur-sm">
        <span class="grid h-9 w-9 shrink-0 place-items-center rounded-full bg-black/20 ring-1 ring-blue-300/40 text-base drop-shadow-[0_2px_3px_rgba(0,0,0,.95)]" aria-hidden="true">📅</span>
        <div class="min-w-0">
          <span class="block text-[0.62rem] font-black uppercase tracking-widest text-cyan-200 drop-shadow-[0_2px_4px_rgba(0,0,0,.95)]">Data</span>
          <p class="truncate text-xs font-bold drop-shadow-[0_2px_4px_rgba(0,0,0,.95)] sm:text-sm">{{ eventConfig.dateLabel }}</p>
        </div>
      </div>

      <div class="flex min-h-[4rem] items-center gap-2 rounded-xl border border-white/10 bg-black/20 px-2.5 backdrop-blur-sm">
        <span class="grid h-9 w-9 shrink-0 place-items-center rounded-full bg-black/20 ring-1 ring-yellow-300/40 text-base drop-shadow-[0_2px_3px_rgba(0,0,0,.95)]" aria-hidden="true">🕖</span>
        <div class="min-w-0">
          <span class="block text-[0.62rem] font-black uppercase tracking-widest text-yellow-200 drop-shadow-[0_2px_4px_rgba(0,0,0,.95)]">Horário</span>
          <p class="truncate text-xs font-bold drop-shadow-[0_2px_4px_rgba(0,0,0,.95)] sm:text-sm">{{ eventConfig.timeLabel }}</p>
        </div>
      </div>

      <a
        class="col-span-2 flex min-h-[4rem] items-center gap-2 rounded-xl border border-white/10 bg-black/20 px-2.5 backdrop-blur-sm"
        :href="eventConfig.mapsUrl"
        target="_blank"
        rel="noopener noreferrer"
        aria-label="Abrir localização da festa no Google Maps"
      >
        <span class="grid h-9 w-9 shrink-0 place-items-center rounded-full bg-black/20 ring-1 ring-red-300/40 text-base drop-shadow-[0_2px_3px_rgba(0,0,0,.95)]" aria-hidden="true">📍</span>
        <div class="min-w-0">
          <span class="block text-[0.62rem] font-black uppercase tracking-widest text-red-200 drop-shadow-[0_2px_4px_rgba(0,0,0,.95)]">Local</span>
          <p class="truncate text-xs font-bold drop-shadow-[0_2px_4px_rgba(0,0,0,.95)] sm:text-sm">{{ eventConfig.location }}</p>
          <p class="truncate text-[0.68rem] text-white/90 drop-shadow-[0_2px_4px_rgba(0,0,0,.95)] sm:text-xs">{{ eventConfig.address }}</p>
        </div>
        <span class="ml-auto text-yellow-300 drop-shadow-[0_2px_4px_rgba(0,0,0,.95)]" aria-hidden="true">↗</span>
      </a>
    </div>

    <!-- Bloco Inferior (Cronômetro, RSVP e Rodapé) -->
    <div class="mt-8 pt-4">
      <div class="grid grid-cols-4 gap-2 rounded-xl border border-white/10 bg-black/20 p-2 backdrop-blur-sm" aria-label="Contagem regressiva">
        <div
          v-for="part in countdownParts"
          :key="part.label"
          class="text-center"
        >
          <strong class="block text-xl font-black tabular-nums text-white drop-shadow-[0_2px_4px_rgba(0,0,0,.95)] sm:text-3xl">
            {{ String(part.value).padStart(2, '0') }}
          </strong>
          <span class="mt-0.5 block text-[0.58rem] font-black uppercase tracking-wider text-yellow-200 drop-shadow-[0_2px_4px_rgba(0,0,0,.95)] sm:text-xs">
            {{ part.label }}
          </span>
        </div>
      </div>

      <div class="mt-3 rounded-xl border border-white/10 bg-black/20 p-3 backdrop-blur-sm">
        <p class="mb-2 text-center text-xs font-black uppercase tracking-[0.14em] text-white drop-shadow-[0_2px_4px_rgba(0,0,0,.95)]">Confirme sua presença</p>
        <div class="grid grid-cols-3 gap-2">
          <button
            v-for="option in rsvpOptions"
            :key="option.value"
            type="button"
            class="min-h-11 rounded-xl border px-1 py-2.5 text-xs font-black uppercase tracking-wide text-white drop-shadow-lg transition duration-200 active:scale-[0.96] sm:text-sm"
            :class="selectedRsvp === option.value ? option.activeClass : option.idleClass"
            :aria-pressed="selectedRsvp === option.value"
            @click="selectRsvp(option)"
          >
            <span class="mr-1 drop-shadow-sm" aria-hidden="true">{{ option.icon }}</span>{{ option.label }}
          </button>
        </div>

        <p
          v-if="rsvpFeedback"
          class="mt-2 text-center text-xs font-medium text-white drop-shadow-lg"
          role="status"
          aria-live="polite"
        >
          {{ rsvpFeedback }}
        </p>
      </div>

      <footer class="mx-auto mt-3 w-fit rounded-full bg-black/30 px-4 py-1 text-center text-[0.68rem] font-medium tracking-wide text-white/90 shadow-lg backdrop-blur-sm drop-shadow-md">
        Criado com exclusividade por
        <a
          class="font-semibold text-white underline decoration-white/60 underline-offset-2 transition hover:decoration-white focus-visible:rounded-sm focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-white"
          href="https://th.app.br"
          target="_blank"
          rel="noopener noreferrer"
        >TH Apps</a>
      </footer>
    </div>

  </div>
</section>
  </main>
</template>
