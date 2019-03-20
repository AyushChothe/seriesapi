<template>
	<div id="home">
		<!--h1>{{data.title}}({{data.year}})</h1>
		<ul>
			<li v-for="(sea,i) in seasons" :key="i">
				Season {{sea[0].season}}
				<ol>
					<li v-for="(s,i) in sea" :key="i">
						{{s.title}}
						<ul>
							<li v-if="get(s.torrents,'480p.url')">
								<a :href="get(s.torrents,'480p.url')">480P</a>
							</li>
							<li v-if="get(s.torrents,'720p.url')">
								<a :href="get(s.torrents,'720p.url')">720p</a>
							</li>
							<li v-if="get(s.torrents,'1080p.url')">
								<a :href="get(s.torrents,'1080p.url')">1080p</a>
							</li>
						</ul>
					</li>
				</ol>
			</li>
		</ul-->
		<a-layout>
			<a-layout-header>
				<h1>{{data.title}}({{data.year}})</h1>
			</a-layout-header>
			<a-layout-content>
				<a-card title="Series" style="min-height:180px" id="parent">
					<a-card-grid class="img">
						<img :src="data.images.poster">
					</a-card-grid>
					<a-card-grid class="data">
						<h1>{{data.title}}</h1>
						{{data.year}}
					</a-card-grid>
					<a-card-grid class="data2" style="padding:23px">
						<h2>Genres</h2>
						<a-tag v-for="(g,i) in data.genres" :key="i">{{upperFirst(g)}}</a-tag>
					</a-card-grid>
					<a-card-grid class="data2">
						<h2>Rating</h2>
						{{data.rating.percentage}}%
					</a-card-grid>
					<a-card-grid class="data2">
						<h2>Runtime</h2>
						{{data.runtime}} Mins
					</a-card-grid>
					<a-card-grid class="data2">
						<h2>Country</h2>
						{{(data.country).toUpperCase()}}
					</a-card-grid>
					<a-card-grid class="data2 data3">
						<h2>Network</h2>
						{{data.network}}
					</a-card-grid>
					<a-card-grid class="data2 data3">
						<h2>Status</h2>
						{{(data.status).toUpperCase()}}
					</a-card-grid>
					<a-card-grid class="data2 data3">
						<h2>Total Seasons</h2>
						{{data.num_seasons}}
					</a-card-grid>
				</a-card>
				<a-tabs :defaultActiveKey="1">
					<a-tab-pane
						class="episodes"
						v-for="(sea,i) in seasons"
						:key="i+1"
						:tab="`Season ${sea[0].season}`"
					>
						<a-card :title="s.title" class="episode" v-for="(s,i) in sea" :key="i">
							<p>
								<b>Season:</b>
								{{s.season}}
							</p>
							<p>
								<b>Episode:</b>
								{{s.episode}}
							</p>
							<p>
								<b>Date:</b>
								{{format(s.first_aired)}}
							</p>
							<p>
								<b>Overview:</b>
								<br>
								{{s.overview}}
							</p>
							<template class="ant-card-actions" slot="actions">
								<a v-if="get(s.torrents,'480p.url')" :href="get(s.torrents,'480p.url')">
									<a-icon type="download"/>480p
								</a>
								<a v-if="get(s.torrents,'720p.url')" :href="get(s.torrents,'720p.url')">
									<a-icon type="download"/>720p
								</a>
								<a v-if="get(s.torrents,'1080p.url')" :href="get(s.torrents,'1080p.url')">
									<a-icon type="download"/>1080p
								</a>
							</template>
						</a-card>
					</a-tab-pane>
				</a-tabs>
			</a-layout-content>
			<a-layout-footer></a-layout-footer>
		</a-layout>
	</div>
</template>

<script>
import _ from "lodash"
import axios from "axios"
import moment from "moment"

export default {
	name: "Home",
	data() {
		return {
			data: {},
			seasons: {}
		}
	},
	methods: {
		getData() {
			axios
				.get(
					"https://tv-v2.api-fetch.website/show/" + this.$route.params.imdbid
				)
				.then(res => {
					this.data = res.data

					let arr = Array()
					let seas = Array()

					res.data.episodes.forEach(ep => {
						seas.push(ep.season)
					})
					seas = _.sortBy(_.uniq(seas), e => {
						return e
					})
					console.log(seas)
					seas.forEach(s => {
						let sea = _.sortBy(
							_.filter(res.data.episodes, { season: s }),
							e => {
								return e.episode
							}
						)
						arr.push(sea)
					})
					this.seasons = arr
				})
				.catch(err => {
					console.log(err)
				})
		},
		get(obj, str) {
			const url = _.get(obj, str)
			return url
		},
		format(timestamp) {
			return moment(timestamp * 1000).format("llll")
		},
		upperFirst(str) {
			const s = _.upperFirst(str)
			return s
		}
	},
	mounted() {
		this.getData()
	}
}
</script>

<style scoped>
.ant-layout-header {
	background: black;
}
.ant-layout-header h1 {
	color: white;
}
.ant-layout-content {
	padding: 0 1%;
	width: 100%;
}
.episodes {
	width: 100%;
	display: flex;
	flex-wrap: wrap;
	flex-direction: row;
	justify-content: space-around;
	align-items: flex-start;
}
.episode {
	padding: 0;
	margin: 10px;
	width: 300px;
}
.img {
	padding: 0;
	width: 30%;
	display: flex;
	justify-content: center;
	align-items: center;
}
.img img {
	width: 100%;
	height: 100%;
	min-height: 180px;
	max-width: 378px;
}
.data {
	width: 70%;
	min-height: 100%;
}
.data2 {
	width: 35%;
	min-height: 100%;
}
@media screen and (max-width: 1024px) {
	.data2 {
		min-height: 100%;
	}
	.img {
		min-height: 456px;
	}
}
@media screen and (max-width: 768px) {
	.data2 {
		min-height: 100%;
	}
	.data3 {
		width: 33.3%;
	}
	.img {
		min-height: 345px;
	}
}
@media screen and (max-width: 420px) {
	.data2 {
		width: 100%;
		min-height: 100%;
	}
	.img {
		min-height: 180px;
	}
}
@media screen and (max-width: 320px) {
	.img img {
		min-height: 180px;
	}
}
</style>