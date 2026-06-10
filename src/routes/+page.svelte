<script>
	import { onMount } from 'svelte';

	// Page & Navigation state
	let activeSection = $state('hero');

	// Quote board / soundboard state
	let quotes = $state([
		{ id: 'q1', text: '24歳、学生です。', meaning: '24 years old, I am a college student.', energy: 'Low', playCount: 0, active: false },
		{ id: 'q2', text: 'いいよ！こいよ！', meaning: 'Sure! Come on!', energy: 'High', playCount: 0, active: false },
		{ id: 'q3', text: 'ぬわああん疲れたもおおおあん', meaning: 'Aargh, I am so incredibly tired!', energy: 'Medium', playCount: 0, active: false },
		{ id: 'q4', text: '王道を往く', meaning: 'Walk the royal/righteous path.', energy: 'Epic', playCount: 0, active: false },
		{ id: 'q5', text: 'ファッ！？', meaning: 'Wh-what?! (Exclamation of shock)', energy: 'High', playCount: 0, active: false },
		{ id: 'q6', text: 'イキスギィ！', meaning: 'Going too far! (Overpowered)', energy: 'Max', playCount: 0, active: false },
		{ id: 'q7', text: 'かしこまり！', meaning: 'Understood! / Right away!', energy: 'Low', playCount: 0, active: false },
		{ id: 'q8', text: '114514', meaning: 'II-YO-KO-I-YO (Sure, come on!)', energy: 'Legendary', playCount: 0, active: false }
	]);

	// Toast notification queue
	let toasts = $state([]);

	// Contact Form state
	let formData = $state({
		name: '',
		email: '',
		serviceType: 'karate',
		message: '',
		hasIcedTea: false
	});
	let isSubmitting = $state(false);
	let submitSuccess = $state(false);

	// Soundwave visualization state
	let waveHeight = $state([12, 24, 18, 32, 16, 28, 14, 22, 10]);
	let intervalId;

	// Trigger soundboard visual effect & toast
	function playQuote(quote) {
		// Increment count
		quote.playCount += 1;
		quote.active = true;

		// Add custom toast
		const newToast = {
			id: Date.now(),
			text: `🔊 「${quote.text}」を発声しました！（意味: ${quote.meaning}）`,
			energy: quote.energy
		};
		toasts = [...toasts, newToast];

		// Remove toast after 4s
		setTimeout(() => {
			toasts = toasts.filter(t => t.id !== newToast.id);
		}, 4000);

		// Disable active glow effect on button after 800ms
		setTimeout(() => {
			quote.active = false;
		}, 800);

		// Animate soundwave wildly for a moment
		let ticks = 0;
		if (intervalId) clearInterval(intervalId);
		intervalId = setInterval(() => {
			waveHeight = waveHeight.map(() => Math.floor(Math.random() * 32) + 8);
			ticks++;
			if (ticks > 12) {
				clearInterval(intervalId);
				waveHeight = [12, 24, 18, 32, 16, 28, 14, 22, 10];
			}
		}, 100);
	}

	// Submit Form mock
	function handleSubmit(e) {
		e.preventDefault();
		isSubmitting = true;

		setTimeout(() => {
			isSubmitting = false;
			submitSuccess = true;
			// Reset form
			formData = {
				name: '',
				email: '',
				serviceType: 'karate',
				message: '',
				hasIcedTea: false
			};
		}, 1500);
	}

	// Scroll spy for menu active highlighting
	onMount(() => {
		const handleScroll = () => {
			const sections = ['hero', 'profile', 'quotes', 'timeline', 'booking'];
			const scrollPos = window.scrollY + 200;

			for (const sec of sections) {
				const el = document.getElementById(sec);
				if (el) {
					const top = el.offsetTop;
					const height = el.offsetHeight;
					if (scrollPos >= top && scrollPos < top + height) {
						activeSection = sec;
						break;
					}
				}
			}
		};

		window.addEventListener('scroll', handleScroll);
		return () => window.removeEventListener('scroll', handleScroll);
	});
</script>

<div class="min-h-screen flex flex-col">
	<!-- Sticky Header -->
	<header class="sticky top-0 z-40 w-full glass-card border-b border-gold-200/50 backdrop-blur-md">
		<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
			<!-- Logo -->
			<a href="#hero" class="flex items-center space-x-2 group">
				<span class="text-2xl font-black bg-gradient-to-r from-gold-600 to-amber-800 bg-clip-text text-transparent transition-all group-hover:scale-105">
					COJI PORTAL
				</span>
				<span class="bg-amber-500 text-white text-[10px] font-bold px-1.5 py-0.5 rounded-full uppercase tracking-wider">
					Official
				</span>
			</a>

			<!-- Navigation -->
			<nav class="hidden md:flex space-x-8 text-sm font-semibold">
				<a href="#hero" class="transition-colors hover:text-gold-700 {activeSection === 'hero' ? 'text-gold-700 underline underline-offset-4 decoration-2' : 'text-slate-600'}">ホーム</a>
				<a href="#profile" class="transition-colors hover:text-gold-700 {activeSection === 'profile' ? 'text-gold-700 underline underline-offset-4 decoration-2' : 'text-slate-600'}">プロフィール</a>
				<a href="#quotes" class="transition-colors hover:text-gold-700 {activeSection === 'quotes' ? 'text-gold-700 underline underline-offset-4 decoration-2' : 'text-slate-600'}">公式語録</a>
				<a href="#timeline" class="transition-colors hover:text-gold-700 {activeSection === 'timeline' ? 'text-gold-700 underline underline-offset-4 decoration-2' : 'text-slate-600'}">略歴</a>
				<a href="#booking" class="transition-colors hover:text-gold-700 {activeSection === 'booking' ? 'text-gold-700 underline underline-offset-4 decoration-2' : 'text-slate-600'}">オファー</a>
			</nav>

			<!-- Quick CTA -->
			<div>
				<a href="#booking" class="bg-gradient-to-r from-amber-500 to-gold-600 hover:from-amber-600 hover:to-gold-700 text-white font-bold py-2 px-5 rounded-full text-xs shadow-md shadow-amber-500/10 transition-transform active:scale-95">
					オファーを送る
				</a>
			</div>
		</div>
	</header>

	<main class="flex-grow">
		<!-- Hero Section -->
		<section id="hero" class="relative overflow-hidden pt-24 pb-20 lg:pt-32 lg:pb-28 flex items-center justify-center">
			<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 relative z-10 grid grid-cols-1 lg:grid-cols-12 gap-12 items-center">
				<div class="lg:col-span-7 text-center lg:text-left space-y-6">
					<div class="inline-flex items-center space-x-2 bg-amber-100/80 border border-amber-200 text-amber-900 rounded-full px-3 py-1 text-xs font-semibold animate-pulse">
						<span>✨ 2026年 オフィシャルサイトグランドオープン</span>
					</div>
					<h1 class="text-4xl sm:text-5xl lg:text-6xl font-black text-slate-900 tracking-tight leading-none">
						王道を往く、<br class="hidden sm:inline" />
						<span class="bg-gradient-to-r from-amber-600 via-gold-500 to-amber-800 bg-clip-text text-transparent">
							伝説の男コウジ
						</span><br />
						の公式ポータル
					</h1>
					<p class="text-base sm:text-lg text-slate-600 max-w-xl mx-auto lg:mx-0">
						日本中に語り継がれるカリスマ、空手部員の野獣先輩（コウジ）の公式サイト。磨き抜かれた肉体、究極のおもてなし精神、そして王道を往く信念をここに集約。
					</p>
					
					<div class="flex flex-col sm:flex-row justify-center lg:justify-start gap-4">
						<a href="#quotes" class="bg-slate-900 hover:bg-slate-800 text-white font-bold px-8 py-3.5 rounded-xl transition-all shadow-lg active:scale-98 flex items-center justify-center space-x-2">
							<span>語録を聞いてみる</span>
							<span class="text-amber-400">⚡</span>
						</a>
						<a href="#profile" class="bg-white/80 hover:bg-white border border-slate-200 text-slate-800 font-bold px-8 py-3.5 rounded-xl transition-all shadow-sm active:scale-98 flex items-center justify-center">
							プロフィールを見る
						</a>
					</div>
				</div>

				<!-- Visual Artwork Container -->
				<div class="lg:col-span-5 flex justify-center">
					<div class="relative w-72 h-72 sm:w-80 sm:h-80 lg:w-96 lg:h-96 animate-float">
						<!-- Glow backdrop -->
						<div class="absolute inset-0 bg-gradient-to-tr from-amber-400 to-yellow-300 rounded-full blur-2xl opacity-40"></div>
						
						<!-- Avatar frame -->
						<div class="relative w-full h-full rounded-3xl overflow-hidden border-4 border-white shadow-2xl glass-card">
							<img src="/avatar.png" alt="Yaju Senpai Karate Art" class="w-full h-full object-cover transform hover:scale-105 transition-transform duration-700" />
							
							<!-- Badge Overlay -->
							<div class="absolute bottom-4 left-4 right-4 bg-slate-900/90 backdrop-blur-md rounded-2xl p-4 text-white border border-white/10">
								<div class="flex items-center justify-between">
									<div>
										<p class="text-[10px] text-amber-400 uppercase tracking-widest font-black">Status</p>
										<p class="text-sm font-bold">24歳・学生 (Karate Athlete)</p>
									</div>
									<div class="text-right">
										<p class="text-[10px] text-amber-400 uppercase tracking-widest font-black">Power Level</p>
										<p class="text-sm font-bold text-yellow-300">114,514 KP</p>
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</section>

		<!-- Profile Dashboard Section -->
		<section id="profile" class="py-20 bg-amber-50/50 relative border-y border-amber-100/50">
			<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
				<div class="text-center max-w-3xl mx-auto mb-16">
					<h2 class="text-sm font-bold text-amber-600 tracking-wider uppercase">Profile</h2>
					<p class="mt-2 text-3xl sm:text-4xl font-extrabold text-slate-900 tracking-tight">オフィシャルステータス</p>
					<p class="mt-4 text-lg text-slate-600">
						文武両道を極め、卓越したもてなしの心（アイスティー）を重んじる彼の知られざる詳細データ。
					</p>
				</div>

				<div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
					<!-- Card 1: Basic Stats -->
					<div class="glass-card rounded-2xl p-8 border border-white shadow-lg flex flex-col justify-between hover:shadow-xl transition-all">
						<div>
							<div class="w-12 h-12 rounded-xl bg-amber-500 text-white flex items-center justify-center text-xl font-bold mb-6">
								🥋
							</div>
							<h3 class="text-xl font-bold text-slate-900 mb-4">基本属性 (Physical Stats)</h3>
							<ul class="space-y-4 text-slate-700 text-sm">
								<li class="flex justify-between border-b border-amber-100 pb-2">
									<span class="font-semibold text-slate-500">名前</span>
									<span class="font-bold">コウジ (野獣先輩)</span>
								</li>
								<li class="flex justify-between border-b border-amber-100 pb-2">
									<span class="font-semibold text-slate-500">年齢</span>
									<span class="font-bold">24歳</span>
								</li>
								<li class="flex justify-between border-b border-amber-100 pb-2">
									<span class="font-semibold text-slate-500">職業</span>
									<span class="font-bold">学生 (大学4年生)</span>
								</li>
								<li class="flex justify-between border-b border-amber-100 pb-2">
									<span class="font-semibold text-slate-500">身長 / 体重</span>
									<span class="font-bold">170cm / 74kg</span>
								</li>
								<li class="flex justify-between pb-2">
									<span class="font-semibold text-slate-500">所属</span>
									<span class="font-bold">空手部</span>
								</li>
							</ul>
						</div>
						<div class="mt-6 pt-4 border-t border-amber-100 text-xs text-amber-600 font-semibold italic text-center">
							「日々の稽古は嘘をつかない」
						</div>
					</div>

					<!-- Card 2: Hobbies & Skills -->
					<div class="glass-card rounded-2xl p-8 border border-white shadow-lg flex flex-col justify-between hover:shadow-xl transition-all">
						<div>
							<div class="w-12 h-12 rounded-xl bg-amber-500 text-white flex items-center justify-center text-xl font-bold mb-6">
								🍵
							</div>
							<h3 class="text-xl font-bold text-slate-900 mb-4">趣味・特技 (Hobbies & Skills)</h3>
							<ul class="space-y-4 text-slate-700 text-sm">
								<li class="flex justify-between border-b border-amber-100 pb-2">
									<span class="font-semibold text-slate-500">趣味</span>
									<span class="font-bold">水泳、ドライブ、屋上日光浴</span>
								</li>
								<li class="flex justify-between border-b border-amber-100 pb-2">
									<span class="font-semibold text-slate-500">特技</span>
									<span class="font-bold">アイスティーの調合、空手(黒帯)</span>
								</li>
								<li class="flex justify-between border-b border-amber-100 pb-2">
									<span class="font-semibold text-slate-500">好きな飲み物</span>
									<span class="font-bold">特製アイスティー (微糖)</span>
								</li>
								<li class="flex justify-between border-b border-amber-100 pb-2">
									<span class="font-semibold text-slate-500">愛車</span>
									<span class="font-bold">白のマークII</span>
								</li>
								<li class="flex justify-between pb-2">
									<span class="font-semibold text-slate-500">座右の銘</span>
									<span class="font-bold">「王道を往く」</span>
								</li>
							</ul>
						</div>
						<div class="mt-6 pt-4 border-t border-amber-100 text-xs text-amber-600 font-semibold italic text-center">
							「おもてなしの心はアイスティーから」
						</div>
					</div>

					<!-- Card 3: Philosophy -->
					<div class="glass-card rounded-2xl p-8 border border-white shadow-lg flex flex-col justify-between hover:shadow-xl transition-all">
						<div>
							<div class="w-12 h-12 rounded-xl bg-slate-900 text-white flex items-center justify-center text-xl font-bold mb-6">
								👑
							</div>
							<h3 class="text-xl font-bold text-slate-900 mb-4">王道の理念 (Philosophy)</h3>
							<p class="text-sm text-slate-600 leading-relaxed">
								部活動における後輩の面倒見の良さや、疲れた体に寄り添う包容力はカリスマと呼ぶにふさわしい。彼が追求する「王道」とは、小手先のテクニックではなく、堂々と真正面から物事に対峙し、人々に元気を与える生き方そのものである。
							</p>
							<div class="mt-6 p-4 bg-amber-100/50 rounded-xl border border-amber-200/50">
								<p class="text-xs font-bold text-slate-900 mb-1">【王道の三箇条】</p>
								<ol class="list-decimal list-inside text-xs text-slate-700 space-y-1">
									<li>礼儀正しく、まずは挨拶から</li>
									<li>疲れた仲間には適切な休息を</li>
									<li>どんな困難にもいいよ、来いよの精神で</li>
								</</ol>
							</div>
						</div>
						<div class="mt-6 pt-4 border-t border-amber-100 text-xs text-slate-600 font-semibold italic text-center">
							※すべてのオファーに誠実に対応します
						</div>
					</div>
				</div>
			</div>
		</section>

		<!-- Interactive Quote Board Section -->
		<section id="quotes" class="py-20 relative overflow-hidden">
			<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 relative z-10">
				<div class="text-center max-w-3xl mx-auto mb-16">
					<h2 class="text-sm font-bold text-amber-600 tracking-wider uppercase">Interactive</h2>
					<p class="mt-2 text-3xl sm:text-4xl font-extrabold text-slate-900 tracking-tight">公式語録サウンドボード</p>
					<p class="mt-4 text-lg text-slate-600">
						ボタンをクリックして、伝説の名言（セリフ）をWeb上で発声・ビジュアライズさせよう！
					</p>
				</div>

				<div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start">
					<!-- Controls & Wave Visualizer -->
					<div class="lg:col-span-4 glass-card rounded-2xl p-6 border border-white shadow-lg space-y-6">
						<h3 class="text-lg font-bold text-slate-900">ボイス・チェッカー</h3>
						<p class="text-xs text-slate-500">
							各ボイスをクリックすると、下のオーディオ波形がリアルタイムに反応します。
						</p>

						<!-- Soundwave simulation -->
						<div class="bg-slate-950 rounded-xl p-6 h-36 flex items-end justify-center space-x-1.5 border border-slate-800 shadow-inner">
							{#each waveHeight as height}
								<div 
									class="w-2.5 bg-gradient-to-t from-amber-500 to-yellow-300 rounded-full transition-all duration-75"
									style="height: {height}%"
								></div>
							{/each}
						</div>

						<div class="text-xs text-slate-500 space-y-2">
							<div class="flex justify-between">
								<span>シミュレーション精度:</span>
								<span class="font-bold text-amber-600">114.514%</span>
							</div>
							<div class="flex justify-between">
								<span>アンプ感度:</span>
								<span class="font-bold text-amber-600">810db</span>
							</div>
						</div>
					</div>

					<!-- Speech Grid -->
					<div class="lg:col-span-8 grid grid-cols-1 sm:grid-cols-2 gap-4">
						{#each quotes as quote}
							<button 
								onclick={() => playQuote(quote)}
								class="relative text-left p-5 rounded-2xl transition-all duration-200 border text-slate-800 flex flex-col justify-between h-32 focus:outline-none focus:ring-2 focus:ring-amber-500 
								{quote.active 
									? 'bg-amber-400 border-amber-500 scale-98 shadow-md text-slate-950 font-black' 
									: 'bg-white/80 border-slate-200 hover:bg-white hover:border-amber-300 hover:-translate-y-0.5 hover:shadow-md'}"
							>
								<!-- Top metadata -->
								<div class="w-full flex justify-between items-center text-[10px] uppercase font-bold text-slate-400">
									<span class="{quote.active ? 'text-slate-900' : 'text-amber-600'}">Energy: {quote.energy}</span>
									<span class="{quote.active ? 'text-slate-900' : 'text-slate-400'}">Count: {quote.playCount}</span>
								</div>

								<!-- Quote main text -->
								<div class="text-base font-bold my-2 {quote.active ? 'text-slate-950' : 'text-slate-800'}">
									「{quote.text}」
								</div>

								<!-- Meaning -->
								<div class="text-[11px] {quote.active ? 'text-slate-800' : 'text-slate-500'} truncate w-full">
									{quote.meaning}
								</div>

								<!-- Floating sound wave icon when clicked -->
								{#if quote.active}
									<span class="absolute top-2 right-2 text-xs animate-ping">🔊</span>
								{/if}
							</button>
						{/each}
					</div>
				</div>
			</div>
		</section>

		<!-- Timeline Section -->
		<section id="timeline" class="py-20 bg-amber-50/50 border-t border-amber-100/50">
			<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
				<div class="text-center max-w-3xl mx-auto mb-16">
					<h2 class="text-sm font-bold text-amber-600 tracking-wider uppercase">History</h2>
					<p class="mt-2 text-3xl sm:text-4xl font-extrabold text-slate-900 tracking-tight">王道を往く軌跡（略歴）</p>
					<p class="mt-4 text-lg text-slate-600">
						空手での活躍から、インターネット界の伝説へと昇華した足跡を振り返ります。
					</p>
				</div>

				<div class="relative max-w-3xl mx-auto">
					<!-- Vertical center line -->
					<div class="absolute left-4 sm:left-1/2 top-0 bottom-0 w-0.5 bg-gradient-to-b from-amber-400 via-amber-300 to-transparent"></div>

					<!-- Timeline Item 1 -->
					<div class="relative flex flex-col sm:flex-row items-start sm:items-center justify-between mb-12">
						<div class="sm:w-5/12 pl-12 sm:pl-0 sm:text-right order-2 sm:order-1">
							<span class="inline-block bg-amber-100 text-amber-800 font-bold px-3 py-1 rounded-full text-xs mb-2">2001年 春</span>
							<h4 class="text-lg font-bold text-slate-900">空手部への電撃入部</h4>
							<p class="text-sm text-slate-600 mt-2">卓越した身体能力と礼儀正しさで空手部へ入部。徹底的な鍛錬により、瞬く間に黒帯を取得。</p>
						</div>
						<!-- Center Badge -->
						<div class="absolute left-2 sm:left-1/2 transform -translate-x-1/2 w-4 h-4 rounded-full bg-amber-500 border-4 border-white shadow-md z-10 order-1"></div>
						<div class="hidden sm:block sm:w-5/12 order-3"></div>
					</div>

					<!-- Timeline Item 2 -->
					<div class="relative flex flex-col sm:flex-row items-start sm:items-center justify-between mb-12">
						<div class="hidden sm:block sm:w-5/12"></div>
						<!-- Center Badge -->
						<div class="absolute left-2 sm:left-1/2 transform -translate-x-1/2 w-4 h-4 rounded-full bg-amber-500 border-4 border-white shadow-md z-10"></div>
						<div class="sm:w-5/12 pl-12 sm:pl-8 order-2">
							<span class="inline-block bg-amber-100 text-amber-800 font-bold px-3 py-1 rounded-full text-xs mb-2">2007年 夏</span>
							<h4 class="text-lg font-bold text-slate-900">インターネット界へ登場</h4>
							<p class="text-sm text-slate-600 mt-2">インターネットの動画共有プラットフォームに姿が現れる。その規格外のインパクトから瞬く間に話題へ。</p>
						</div>
					</div>

					<!-- Timeline Item 3 -->
					<div class="relative flex flex-col sm:flex-row items-start sm:items-center justify-between mb-12">
						<div class="sm:w-5/12 pl-12 sm:pl-0 sm:text-right order-2 sm:order-1">
							<span class="inline-block bg-amber-100 text-amber-800 font-bold px-3 py-1 rounded-full text-xs mb-2">2012年 秋</span>
							<h4 class="text-lg font-bold text-slate-900">ニコニコ動画における黄金期</h4>
							<p class="text-sm text-slate-600 mt-2">MAD動画やファンアートなど、二次創作の中心的ミームとして定着。その愛される人柄で、ネット文化の絶対的象徴となる。</p>
						</div>
						<!-- Center Badge -->
						<div class="absolute left-2 sm:left-1/2 transform -translate-x-1/2 w-4 h-4 rounded-full bg-amber-500 border-4 border-white shadow-md z-10 order-1"></div>
						<div class="hidden sm:block sm:w-5/12 order-3"></div>
					</div>

					<!-- Timeline Item 4 -->
					<div class="relative flex flex-col sm:flex-row items-start sm:items-center justify-between">
						<div class="hidden sm:block sm:w-5/12"></div>
						<!-- Center Badge -->
						<div class="absolute left-2 sm:left-1/2 transform -translate-x-1/2 w-4 h-4 rounded-full bg-amber-500 border-4 border-white shadow-md z-10"></div>
						<div class="sm:w-5/12 pl-12 sm:pl-8 order-2">
							<span class="inline-block bg-slate-900 text-amber-400 font-bold px-3 py-1 rounded-full text-xs mb-2">2026年 現在</span>
							<h4 class="text-lg font-bold text-slate-900">公式ポータルサイト開設</h4>
							<p class="text-sm text-slate-600 mt-2">ついに公式ポータルサイトが誕生。24歳、学生のまま不変のカリスマとして王道を走り続けている。</p>
						</div>
					</div>
				</div>
			</div>
		</section>

		<!-- Booking / Offer Form Section -->
		<section id="booking" class="py-20 relative overflow-hidden">
			<div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 relative z-10">
				<div class="text-center mb-12">
					<h2 class="text-sm font-bold text-amber-600 tracking-wider uppercase">Contact</h2>
					<p class="mt-2 text-3xl sm:text-4xl font-extrabold text-slate-900 tracking-tight">出演・コラボレーションオファー</p>
					<p class="mt-4 text-base text-slate-600">
						空手の指導、アイスティー調合指導、ドライブへの同行など、お仕事のご依頼はこちらから。
					</p>
				</div>

				<div class="glass-card rounded-3xl p-8 sm:p-12 border border-white shadow-2xl">
					{#if submitSuccess}
						<div class="text-center py-10 space-y-4">
							<div class="w-16 h-16 bg-amber-100 text-amber-600 rounded-full flex items-center justify-center text-3xl mx-auto animate-bounce">
								🍵
							</div>
							<h3 class="text-2xl font-bold text-slate-900">オファーを送信しました！</h3>
							<p class="text-slate-600 max-w-md mx-auto">
								お問い合わせありがとうございます。114514時間以内に王道的な対応でご返信いたします。
							</p>
							<button 
								onclick={() => submitSuccess = false}
								class="mt-6 bg-slate-900 hover:bg-slate-800 text-white font-bold py-2.5 px-6 rounded-xl transition-all"
							>
								新しいオファーを作成
							</button>
						</div>
					{:else}
						<form onsubmit={handleSubmit} class="space-y-6">
							<div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
								<div>
									<label for="name" class="block text-sm font-bold text-slate-700 mb-1">お名前 / 企業名</label>
									<input 
										type="text" 
										id="name" 
										bind:value={formData.name} 
										required 
										placeholder="例: 三浦 先輩" 
										class="w-full rounded-xl border-slate-300 shadow-sm focus:border-amber-500 focus:ring-amber-500 bg-white/70"
									/>
								</div>
								
								<div>
									<label for="email" class="block text-sm font-bold text-slate-700 mb-1">メールアドレス</label>
									<input 
										type="email" 
										id="email" 
										bind:value={formData.email} 
										required 
										placeholder="例: senpai@114514.com" 
										class="w-full rounded-xl border-slate-300 shadow-sm focus:border-amber-500 focus:ring-amber-500 bg-white/70"
									/>
								</div>
							</div>

							<div>
								<label for="serviceType" class="block text-sm font-bold text-slate-700 mb-1">ご依頼内容</label>
								<select 
									id="serviceType" 
									bind:value={formData.serviceType} 
									class="w-full rounded-xl border-slate-300 shadow-sm focus:border-amber-500 focus:ring-amber-500 bg-white/70"
								>
									<option value="karate">空手の特別稽古・合同トレーニング</option>
									<option value="icetea">アイスティー調合およびおもてなし研修</option>
									<option value="drive">白のマークIIで行く日光浴ツアー</option>
									<option value="media">TV・動画への出演依頼</option>
									<option value="other">その他 (王道を往く内容)</option>
								</select>
							</div>

							<div>
								<label for="message" class="block text-sm font-bold text-slate-700 mb-1">詳細メッセージ</label>
								<textarea 
									id="message" 
									rows="4" 
									bind:value={formData.message} 
									required 
									placeholder="例: 空手部員に対するおもてなしのご相談..." 
									class="w-full rounded-xl border-slate-300 shadow-sm focus:border-amber-500 focus:ring-amber-500 bg-white/70"
								></textarea>
							</div>

							<!-- Special checklist joke field -->
							<div class="bg-amber-100/60 rounded-xl p-4 border border-amber-200/50 flex items-start space-x-3">
								<div class="flex items-center h-5">
									<input 
										id="hasIcedTea" 
										type="checkbox" 
										bind:checked={formData.hasIcedTea} 
										class="h-4 w-4 rounded border-slate-300 text-amber-600 focus:ring-amber-500"
									/>
								</div>
								<div class="text-xs">
									<label for="hasIcedTea" class="font-bold text-slate-800">
										冷たいアイスティーを準備しています
									</label>
									<p class="text-slate-500 mt-0.5">※チェックを入れると、オファーの成立確率と返信速度が格段にアップします。</p>
								</div>
							</div>

							<button 
								type="submit" 
								disabled={isSubmitting}
								class="w-full bg-slate-900 hover:bg-slate-800 disabled:bg-slate-400 text-white font-bold py-4 rounded-xl transition-all shadow-lg hover:shadow-xl active:scale-98 flex items-center justify-center space-x-2"
							>
								{#if isSubmitting}
									<span class="animate-spin text-amber-400">⏳</span>
									<span>オファー送信中...</span>
								{:else}
									<span>王道オファーを送信する (114514)</span>
								{/if}
							</button>
						</form>
					{/if}
				</div>
			</div>
		</section>
	</main>

	<!-- Footer -->
	<footer class="bg-slate-900 text-white py-12 border-t border-slate-800">
		<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 text-center space-y-6">
			<div class="text-xl font-black bg-gradient-to-r from-amber-400 to-yellow-300 bg-clip-text text-transparent">
				COJI PORTAL
			</div>
			<p class="text-xs text-slate-500 max-w-md mx-auto leading-relaxed">
				当サイトは、インターネットミーム「野獣先輩（コウジ）」をテーマにした、クリーンでユーモアあふれるジョーク/ファンサイトです。
			</p>
			<p class="text-xs text-slate-600">
				&copy; 2026 Yaju Senpai Fan Portal. All Rights Reserved. Walk the royal road.
			</p>
		</div>
	</footer>
</div>

<!-- Floating Toasts -->
<div class="fixed bottom-6 right-6 z-50 flex flex-col space-y-3 pointer-events-none">
	{#each toasts as toast (toast.id)}
		<div class="glass-card-dark text-white p-4 rounded-2xl shadow-xl flex items-center space-x-3 max-w-sm border border-amber-500/20 transform translate-y-0 transition-all duration-300 animate-bounce">
			<span class="text-base text-yellow-400">⚡</span>
			<div class="text-xs font-semibold">
				<p class="text-[10px] text-amber-400 uppercase tracking-widest font-black">Voice Log ({toast.energy})</p>
				<p class="mt-0.5">{toast.text}</p>
			</div>
		</div>
	{/each}
</div>

<style>
	/* Any additional page-specific styles can be added here */
</style>

