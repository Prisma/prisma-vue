<template>
  <div class="container">
    <div class="resi-header d-print-none">
      <img v-if="$route.params.slug === 'feron'"
      	   :src="require(`@/assets/img/residents/feron.jpg`)" class="rounded-circle">
      <img v-if="$route.params.slug === 'briard'"
      	   :src="require(`@/assets/img/residents/briard.jpg`)" class="rounded-circle">
      <img v-if="$route.params.slug === 'felix'"
      	   :src="require(`@/assets/img/residents/felix.jpg`)" class="rounded-circle">
      <img v-if="$route.params.slug === 'lea'"
      	   :src="require(`@/assets/img/residents/lea.jpg`)" class="rounded-circle">
      <img v-if="$route.params.slug === 'luc'"
      	   :src="require(`@/assets/img/residents/luc.jpg`)" class="rounded-circle">
      <img v-if="$route.params.slug === 'masure'"
      	   :src="require(`@/assets/img/residents/masure.jpg`)" class="rounded-circle">
      <img v-if="$route.params.slug === 'pelgrims'"
      	   :src="require(`@/assets/img/residents/pelgrims.jpg`)" class="rounded-circle">
      <img v-if="$route.params.slug === 'vooght'"
      	   :src="require(`@/assets/img/residents/vooght.jpg`)" class="rounded-circle">
      <img v-if="$route.params.slug !== 'feron' &&
      		 $route.params.slug !== 'briard' &&
		 $route.params.slug !== 'felix' &&
      		 $route.params.slug !== 'lea' &&
      		 $route.params.slug !== 'luc' &&
      		 $route.params.slug !== 'masure' &&
      		 $route.params.slug !== 'pelgrims' &&
      		 $route.params.slug !== 'vooght'"
      	   class="rounded-circle" :src="require(`@/assets/img/residents/no-image.jpg`)" >
      <h1 class="resi-header-displayname">{{ $route.params.slug }}</h1>
    </div>

    <div class="resi-nav">
      <nav id="navbar-albums" class="nav d-print-none justify-content-center">
	<a
	  v-for="album in albums"
	  v-bind:key="album.id"
	  :href="'#' + album.title"
	  class="nav-link">
	  {{ album.title }}
	</a>
      </nav>
    </div>

    <!-- Modal to add a story -->
    <b-modal ref="addModalRef" class="story-add" id="addStoryModal"
	     hide-footer title="Voeg verhaal toe">

      <div class="media">
	<img class="mr-3 rounded-circle" src="http://i.pravatar.cc/48"
	     alt="Avatar Afbeelding">
	<div class="media-body">
	  <b-form-textarea id="textarea1"
			   v-model="newStory"
			   :no-resize=true
			   placeholder="Vertel het verhaal"
			   :rows="3"
			   maxlength="250"
			   :max-rows="6">
	  </b-form-textarea>
	  <p><strong>{{ charactersRemaining }}</strong> resterende tekens</p>
	</div>
      </div>

      <b-form @submit="onSubmit" enctype="multipart/form-data" novalidate>

	<div class="story-add-media d-flex justify-content-start mb-4">
	  <b-btn variant="light" size="sm"
		 class="d-flex align-items-center mr-2"
		 @click="showMediaPreview('photo')">
	    <i class="material-icons mr-2">camera_alt</i> Foto
	  </b-btn>

	  <b-btn variant="light" size="sm" class="d-flex align-items-center"
		 @click="showMediaPreview('video')">
	    <i class="material-icons mr-2">movie</i> Video
	  </b-btn>
	</div>

	<div class="story-add-photopreview" v-if="image && seen == false">
	  <div class="card">
	    <img :src="image" class="img-responsive" width="125" height="125">
	    <div class="card-img-overlay text-right p-1">
              <a href="#" @click="hideMedia" class="close text-white">×</a>
	    </div>
	  </div>
        </div>

	<div v-if="previewType == 'photo' && seen" class="dropbox">
	  <input type="file"
		 :name="uploadFieldName"
		 :disabled="isSaving"
		 @change="onFileChange"
		 accept="image/*"
		 class="input-file">

	  <p>
	    <i class="material-icons">arrow_upward</i> <br>
	    Sleep uw bestand hier of klik om te bladeren.
	  </p>
	</div>

	<div class="mt-4" v-if="previewType == 'video'">
	  {{ this.reset() }}

	  <b-form-group id=""
			label=""
			label-for="">

	    <label for="youtubeUrl">YouTube Link:</label>
	    <b-form-input
	      id="youtubeUrl"
	      type="text"
	      placeholder="https://www.youtube.com/watch?v=ffSnk4v3aeg"
	      @input="addYoutube"
	      v-model="youtubeUrl">
	    </b-form-input>
	  </b-form-group>
	</div>

	<b-form-group id="CategoryInputGroup"
		      label-for="categoryInput">
	  <b-form-select id="categoryInput" v-model="form.newAlbum" :options="categories"
			 required>
	  </b-form-select>
      	</b-form-group>

	<hr>
	<div class="row">
	  <div class="col-12">
	    <b-btn type="submit" variant="outline-primary" block>Voeg Toe</b-btn>
	  </div>
	</div>
      </b-form>
    </b-modal>

    <section v-if="errored || this.albums.length == 0" v-cloak class="text-center d-print-none row">
      <div class="col-md-8 mx-auto">
	<p class="lead lead-lg">Wie is {{ $route.params.slug }}?</p>
	<p>
	  Help uw families {{ $route.params.slug }} te leren kennen
	  door middel van verhalen over zijn leven.
	</p>
	<b-btn variant="primary"
	       class="btn-circle d-flex justify-content-center
		      align-items-center mb-1 d-print-none mx-auto"
	       @click="showAddModal">
	  <i class="material-icons">add</i>Verhaal toevoegen
	</b-btn>
	<a href="https://prisma.care/levensverhaal-posters/">
	  <b-img thumbnail src="https://prisma.care/wp-content/uploads/2018/05/prisma-poster-maken-pelgrims.jpg" alt="Poster maken" />
	</a>
	<a href="https://prisma.care/levensverhaal-posters/">Hoe maak je een poster?</a>
	<hr class="mt-4">
	<h2 class="mb-3">Hulp van je familie</h2>
	<p>Wie heeft er nog foto's van <strong>{{ $route.params.slug
	    }}</strong>, en kan de verhalen aanvullen?</p>
	<b-btn href="/invite" variant="outline-primary" class="btn-add">
	  <i class="material-icons md-18 mr-2">email</i>Nodig familie uit
	</b-btn>
      </div> <!-- ./col-md-8 -->
    </section>

    <section v-else>
      <div v-if="loading">Loading...</div>
      <div v-else>

	<!-- Modal to edit a story -->
	<b-modal ref="editModalRef" id="editStoryModal" hide-footer
		 title="Bewerk tekst">
	  <b-form-textarea id="formEdit"
			   v-model="editStory"
			   placeholder="Vertel het verhaal"
			   :rows="3"
			   :max-rows="6">
	  </b-form-textarea>
	  <hr>
	  <div class="row">
	    <div class="col-12">
	      <b-btn variant="outline-primary" block>Bewerk tekst</b-btn>
	    </div>
	  </div>
	</b-modal>

	<div class="story-container">

	  <b-btn
	    v-if="this.albums.length > 0"
	    variant="primary" size="sm"
	    id="addStoryBtn"
	    class="btn btn-primary btn-sm d-flex justify-content-center align-items-center mb-1 d-print-none"
	    @click="showAddModal">
	    <i class="material-icons">add</i>Verhaal toevoegen
	  </b-btn>

	  <div v-for="(album, index) in albums"
	       v-bind:key="album.id + 'stories'">
	    <div class="story-category" :id="album.title">
	      <div class="story-category-header">
		<h2>{{ album.title }}</h2>
	      </div>
	      <div class="row">
		<div v-for="story in album.stories"
		     class="story col-md-6 col-lg-4 d-print-none"
		     v-bind:key="story.id"
		     v-bind:class="{checked: checkedStories.includes(story.id),
				   'd-print-block':
				   checkedStories.includes(story.id)}">

		  <div v-if="story['type'] == 'image' || story['type'] == 'youtube'">

		    <b-card title="Card Title"
			    :img-src="story.type === 'youtube' ? getYouTubeThumb(story.source) : 'undefined'"
			    img-alt="Image"
			    img-top
			    :id="'story-' + story.id"
			    :key="story.id"
			    :ref="story.id"
			    no-body
			    class="d-flex align-self-center">

		      <b-badge v-if="story['Year']" variant="dark" class="story-year">
			{{ story['Year'] }}
		      </b-badge>

		      <b-btn variant="primary" @click="index = story['index']"
			     v-if="story['type'] == 'youtube'"
			     class="btn-play d-flex justify-content-center align-items-center">
			<i class="material-icons md-32 mx-auto">play_arrow</i>
		      </b-btn>

		      <b-btn variant="primary" @click="index = story['index']"
			     v-if="story['type'] == 'image'"
			     class="btn-zoom d-flex justify-content-center align-items-center">
			<i class="material-icons md-32 mx-auto">zoom_out_map</i>
		      </b-btn>

		      <div class="card-img-overlay">
			<div class="d-flex align-items-center">
			  <input type="checkbox" :id="story.id" :value="story.id" v-model="checkedStories" class="checkbox" number>
			  <label class="form-check-label" :for="story.id">Selecteer</label>
			</div>
		      </div>
		    </b-card>
		  </div>

		  <p v-if="story['type'] == 'image' ||
			   story['type'] == 'youtube'"
		     class="mt-3">{{ story['description'] }}</p>

		  <div class="card card_text"
		       v-if="!story['type']">
		    <div class="card-body">
		      {{ story['description'] }}
		    </div>
		  </div>

		  <b-btn v-if="story['description'] && !story['source']"
			 variant="outline-light" size="sm"
			 class=" d-flex justify-content-center align-items-center mb-2"
			 @click="showEditModal">
		    <i class="material-icons md-18 mr-2">add_photo_alternate</i> Voeg beeld toe <em>TODO</em>
		  </b-btn>

		  <b-btn v-if="story['description']"
			 variant="outline-light" size="sm"
			 class="btn-edit d-flex justify-content-center align-items-center"
			 @click="showEditModal">
		    <i class="material-icons md-18 mr-2">edit</i> Pas tekst aan
		  </b-btn>
		  <b-btn v-if="!story['description']"
			 variant="outline-light" size="sm"
			 class="btn-add d-flex justify-content-center align-items-center"
			 @click="showEditModal">
		    <i class="material-icons md-18 mr-2">edit</i> Voeg tekst toe
		  </b-btn>
		</div> <!-- ./v-for="(album, index) in albums" -->
	      </div> <!-- ./row -->
	    </div> <!-- ./story-category -->
	  </div> <!-- ./story-container -->

	  <div class="container d-flex text-muted">
	    <div class="mr-auto d-flex align-items-center">
	      <input type="checkbox" id="selectAllCheckbox" v-model="selectAll" class="mr-1">
	      <span v-if="checkedStories.length == 0">
		Selecteer de verhalen die je wilt printen
	      </span>

	      <span>
		<b-badge v-if="checkedStories.length > 0" pill variant="primary">
		  {{ checkedStories.length }}
		</b-badge>

		geselecteerd
	      </span>
	    </div>
	    <a href="javascript:window.print()" class="btn btn-sm btn-primary d-flex align-items-center" :disabled="checkedStories.length > 0"><i class="material-icons mr-2">print</i> Print</a>
	  </div>
	</div>
      </div>
    </section>
  </div> <!-- container -->
</template>

<script>
import axios from 'axios';

const MAX_INPUT_LENGTH=250;

export default {
  data () {
    return {
      albums: [],
      categories: [
        { value: null, text: 'Kies album' },
        { value: null, text: 'Familie en vrienden' },
        { value: null, text: 'Gewoonten en tradities' },
        { value: null, text: 'Muziek' },
        { value: null, text: 'Vrije tijd' },
        { value: null, text: 'School en werk' },
        { value: null, text: 'Spel en hobbies' },
        { value: null, text: 'Sport' }
      ],
      checkedStories: [],
      editStory: '',
      errored: false,
      form: {
        newAlbum: null
      },
      image: null,
      index: null,
      loading: false,
      newAlbum: '',
      newStory: '',
      previewType: false,
      seen: true,
      stories: [],
      text: '',
      uploadError: null,
      uploadFieldName: 'photos',
      uploadedFile: null,
      url: 'https://api.prisma.care/v1',
      youtubeUrl: ''
    };
  },
  mounted () {
    this.loadStories();
    this.reset();
  },
  computed: {
    charactersRemaining: function() {
      return MAX_INPUT_LENGTH - this.newStory.length;
    },
    tweetIsOutOfRange: function() {
      return this.charactersRemaining == MAX_INPUT_LENGTH
	|| this.charactersRemaining < 0;
    },
    selectAll: {
      get: function () {
        return this.stories ? this.checkedStories.length == this.albums.length : false;
      },
      set: function (value) {
        var checkedStories = [];
        if (value) {
          this.stories.forEach((story) => {
            checkedStories.push(story.id);
          });
        }
        this.checkedStories = checkedStories;
      }
    },
    isValid() {
      return (this.newStory != '' && this.newAlbum != '') ? false : 'disabled';
    }
  },
  methods: {
    addYoutube() {
      this.videoAdded = true;
    },
    createImage(file) {
      var image = new Image();
      var reader = new FileReader();

      reader.onload = (e) => {
        this.image = e.target.result;
      };

      reader.readAsDataURL(file);
    },
    getYouTubeThumb(url) {
      const id = this.getYouTubeID(url);
      if (id) {
	return `https://img.youtube.com/vi/${id}/hqdefault.jpg`;
      } else {
	return null;
      }
    },
    hideAddModal () {
      this.$refs.addModalRef.hide()
    },
    hideEditModal () {
      this.$refs.editModalRef.hide()
    },
    hideMedia() {
      this.reset();
      this.previewType = false;
    },
    onFileChange(e) {
      var files = e.target.files || e.dataTransfer.files;

      if (!files.length)
        return;

      this.createImage(files[0]);
      this.seen = false
    },
    onSubmit (evt) {
      evt.preventDefault();
      alert(JSON.stringify(this.form));
    },
    reset() {
      this.uploadError = null;
      this.uploadedFiles = [];
      this.seen = true;
    },
    showAddModal() {
      this.$refs.addModalRef.show()
    },
    showEditModal() {
      this.$refs.editModalRef.show()
    },
    showMediaPreview(mediaType) {
      this.previewType = mediaType;
    },
    getYouTubeID(url) {
      const match = /v=(\w*)$/.exec(url);
      if (match) {
	return match[1];
      } else {
	return null;
      }
    },
    loadStories() {
      var appId = 'appzWizY3DXnCjpgh';
      var appKey = 'keyuzHdBFw9QQKZCC';
      var slug = this.$route.params.slug;

      if (slug == "georgetteveekmans") {
        slug = "GeorgetteVeekmans"
      } else if (slug == "rosemariedrouet") {
        slug = "RoseMarieDrouet"
      } else if (slug == "mariejoseemertens") {
        slug = "MarieJoséeMertens"
      } else if (slug == "rosaandries") {
        slug = "RosaAndries"
      } else if (slug == "louisadevos") {
        slug = "devos"
      } else if (slug == "magdawouters") {
        slug = "Wouters"
      } else if (slug == "feron") {
	slug = "Feron"
      } else {
	slug = "Lea"
      }

      axios.
	post(`${this.url}/user/signin`, {
	  "email": "thorgalle@gmail.com",
	  "password": "flipflopflap"
	}).then(response => {
	  if (response.status === 200) {
	    this.token = response.data.response.token;
	    this.userId = response.data.response.id;
	    this.patientId = response.data.response.patients.length ? response.data.response.patients[0].patient_id : undefined;
	    return axios.get(
	      `	${this.url}/patient/${this.patientId}/album`, {
		headers: {
		  Authorization: "Bearer " + this.token
		}
	      });
	  } else {
	    return Promise.reject("login fail");
	  }
	})
	.then(response => {
	  this.albums = response.data.response;

	  this.albums.forEach((album) => {
	    album.stories.sort((story1, story2) => Date.parse(story1.createdAt.date) > Date.parse(story2.createdAt.date));
	  });

	  this.albums.forEach((album, outerIndex) => {
	    album.stories = album.stories.map((v, i) => {
	      v.index = outerIndex + i;
	      return v;
	    })
	  })

	  this.albums.forEach(album => {
	    album.stories.forEach((story) => {
	      if (story) {
		const slide = {
		  title: story.description,
		}

		if (story.type === 'youtube') {
		  const ytid = this.getYouTubeID(story.source);
		  slide.href = story.source;
		  if (ytid) {
		    slide.poster = this.getYouTubeThumb(story.source);
		    slide.youtube = ytid;
		  }
		  slide.type = 'text/html';
		} else if (story.type === 'image') {
		  slide.href = story.source;
		  slide.type = 'image/jpeg';

		  let getImg = new Promise((resolve, reject ) => {
		    var oReq = new XMLHttpRequest();
		    oReq.open("GET", story.source, true);
		    oReq.setRequestHeader('Authorization', "Bearer " + this.token);
		    oReq.responseType = "arraybuffer";
		    oReq.onload = function (oEvent) {
		      var arrayBuffer = oReq.response;
		      resolve(oReq);
		    };
		    oReq.send(null);
		  });

		  getImg.then((response) => {
		    const type = response.getResponseHeader('content-type');
		    document.querySelector(`#story-${story.id} > img`).src = arrayBufferToDataUrl(response.response, type);
		  });
		}

		function arrayBufferToDataUrl(buffer, type) {
		  const base64 = btoa([].reduce.call(new Uint8Array(buffer), (p, c) => p + String.fromCharCode(c), ''));
		  return `data:${type};base64,${base64}`;
		};
	      }
	    })
	  })
	})
	.catch(error => {
          console.log(error);
          this.errored = true;
	})
	.finally(() => this.loading = false);
    },
  }
};
</script>
