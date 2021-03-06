<template>
  <div class="MainPage">
    <div class="Header mb-3">
      <page-header :icon="headerItem.icon">
        {{ headerItem.title }}
      </page-header>
      <div class="UpdatedAt">
        <span>{{ $t('最終更新') }} </span>
        <time :datetime="lastUpdatedAtISO">{{ lastUpdatedAt }}</time>
      </div>
      <div v-if="!['ja', 'ja-basic'].includes($i18n.locale)" class="Annotation">
        <span>{{ $t('注釈') }} </span>
      </div>
    </div>
    <header-card class="md-4" />
    <breaking-news class="mb-4" :items="newsItems" />
    <fukui-paper-news class="mb-4" />
    <whats-new-japan class="mb-4" :items="japanItems" />
    <static-info
      class="mb-4"
      :url="localePath('/flow')"
      target="_blank"
      :text="
        $t('自分や家族の症状に不安や心配がある方はこちらから確認いただけます')
      "
      :btn-text="$t('感染確認フローへ')"
    />
    <v-row class="DataBlock">
      <!-- <youtube-card /> -->
      <confirmed-cases-number-card />
      <hospitalized-patients-card />
      <confirmed-cases-details-card />
      <each-sex-age-number-positive-card />
      <inspection-persons-number-card />
      <hospital-beds-number-card />
      <confirmed-cases-attributes-card />
      <information-number-card />
    </v-row>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { MetaInfo } from 'vue-meta'
import PageHeader from '@/components/PageHeader.vue'
// トップページ／ヘッダーカード
import HeaderCard from '@/components/HeaderCard.vue'
// 速報
import BreakingNews from '@/components/BreakingNews.vue'
// import WhatsNew from '@/components/WhatsNew.vue'
import WhatsNewJapan from '@/components/WhatsNewJapan.vue'
import FukuiPaperNews from '@/components/FukuiPaperNews.vue'
// import FukuiNews from '@/components/FukuiNews.vue'
import StaticInfo from '@/components/StaticInfo.vue'
import News from '@/data/fukui_news.json'
import JapanNews from '@/data/japan_news.json'
import BreakingNewsData from '@/data/breaking_news.json'
// 陽性患者数
import ConfirmedCasesNumberCard from '@/components/cards/ConfirmedCasesNumberCard.vue'
// 入院患者数
import HospitalizedPatientsCard from '@/components/cards/HospitalizedPatientsCard.vue'
// 陽性患者の属性
import ConfirmedCasesAttributesCard from '@/components/cards/ConfirmedCasesAttributesCard.vue'
// 検査陽性者の状況
import ConfirmedCasesDetailsCard from '@/components/cards/ConfirmedCasesDetailsCard.vue'
// 検査実施人数
import InspectionPersonsNumberCard from '@/components/cards/InspectionPersonsNumberCard.vue'
// 病床数
import HospitalBedsNumberCard from '@/components/cards/HospitalBedsNumberCard.vue'
// Youtube
// import YoutubeCard from '@/components/cards/YoutubeCard.vue'

// 検査実施人数
import InspectionPersons from '@/data/inspection_persons.json'
// 検査陽性者の状況
import InspectionsSummary from '@/data/inspection_summary.json'
// 感染症病床使用率
import HospitalBeds from '@/data/hospital_beds.json'
// 陽性患者数
import PatientsSummary from '@/data/patients_summary.json'
//  陽性患者の属性, 年代別の陽性患者数
import Patients from '@/data/patients.json'

// お問い合わせ件数
import Contacts from '@/data/contacts.json'

// コールセンターお問合せ件数
import InformationNumberCard from '@/components/cards/InformationNumberCard.vue'

// 男女年代別の陽性患者数
import EachSexAgeNumberPositiveCard from '@/components/cards/EachSexAgeNumberPositiveCard.vue'

// import TestedCasesDetailsCard from '@/components/cards/TestedCasesDetailsCard.vue'

// import TestedNumberCard from '@/components/cards/TestedNumberCard.vue'
// import ConsultationDeskReportsNumberCard from '@/components/cards/ConsultationDeskReportsNumberCard.vue'
// import MetroCard from '@/components/cards/MetroCard.vue'
// import AgencyCard from '@/components/cards/AgencyCard.vue'
import {
  convertDatetimeToISO8601Format,
  getCommonStyleDateString
} from '@/utils/formatDate'

export default Vue.extend({
  components: {
    PageHeader,
    HeaderCard,
    BreakingNews,
    // FukuiNews,
    FukuiPaperNews,
    // WhatsNew,
    WhatsNewJapan,
    StaticInfo,
    ConfirmedCasesNumberCard,
    HospitalizedPatientsCard,
    ConfirmedCasesDetailsCard,
    InspectionPersonsNumberCard,
    // TestedCasesDetailsCard,
    ConfirmedCasesAttributesCard,
    HospitalBedsNumberCard,
    InformationNumberCard,
    EachSexAgeNumberPositiveCard
    // YoutubeCard,
    // TestedNumberCard,
    // ConsultationDeskReportsNumberCard,
    // MetroCard,
    // AgencyCard
  },
  data() {
    return {
      method: 'default',
      headerItem: {
        icon: 'mdi-chart-timeline-variant',
        title: this.$t('福井県内の最新感染動向')
      },
      newsItems: News.newsItems,
      japanItems: JapanNews.japanItems,
      BreakingItems: BreakingNewsData.items
    }
  },

  computed: {
    lastUpdatedAt(): string {
      const dates = [
        PatientsSummary.date,
        InspectionsSummary.date,
        Patients.date,
        InspectionPersons.date,
        Contacts.date,
        HospitalBeds.date
      ]
      // 辞書順でソート
      dates.sort().reverse()

      return getCommonStyleDateString(dates[0])
    },
    lastUpdatedAtISO() {
      // this as any -> https://github.com/vuejs/vue/issues/8721
      return convertDatetimeToISO8601Format((this as any).lastUpdatedAt)
    }
  },
  head(): MetaInfo {
    return {
      title: this.$t('福井県内の最新感染動向') as string,
      __dangerouslyDisableSanitizers: ['script'],
      script: [
        {
          innerHTML: `{
            "@context": "https://schema.org",
            "@graph":[
              {
                "@type": "Article",
                "@id":"https://covid19-fukui.com/",
                "isPartOf":{
                  "@id":"https://covid19-fukui.com/"
                },
                "mainEntityOfPage": "https://covid19-fukui.com/",
                "headline": "福井県内の最新感染動向 | 福井県公認 新型コロナウイルス感染症対策サイト",
                "description": "当サイトは新型コロナウイルス感染症 (COVID-19) に関する最新情報を提供するために、福井高専卒のエンジニアが開設したものです",
                "image": "https://covid19-fukui.com/official_ogp.png",  
                "author": {
                  "@type": "Person",
                  "name": "野村弘樹（福井高専卒）"
                },  
                "publisher": {
                  "@type": "Organization",
                  "name": "福井県版 新型コロナウィルス感染症対策サイト 運営管理人",
                  "logo": {
                    "@type": "ImageObject",
                    "url": "https://covid19-fukui.com/favicon.png",
                    "width": 48,
                    "height": 48
                  }
                },
                "datePublished": "2020-03-23",
                "dateModified": "2020-04-12"
              },
              {
                "@type": "BreadcrumbList", 
                "itemListElement": [{
                  "@type": "ListItem", 
                  "position": 1, 
                  "name": "検査陽性者の状況",
                  "item": "https://covid19-fukui.com/cards/details-of-confirmed-cases"  
                },{
                  "@type": "ListItem", 
                  "position": 2, 
                  "name": "感染症病床使用率",
                  "item": "https://covid19-fukui.com/cards/hospital-beds-number-card"  
                },{
                  "@type": "ListItem", 
                  "position": 3, 
                  "name": "陽性患者数",
                  "item": "https://covid19-fukui.com/cards/number-of-confirmed-cases"  
                },{
                  "@type": "ListItem", 
                  "position": 4, 
                  "name": "陽性患者の属性一覧",
                  "item": "https://covid19-fukui.com/cards/attributes-of-confirmed-cases"  
                },{
                  "@type": "ListItem", 
                  "position": 5, 
                  "name": "検査実施人数",
                  "item": "https://covid19-fukui.com/cards/number-of-inspection-persons"  
                },{
                  "@type": "ListItem", 
                  "position": 6, 
                  "name": "当サイトについて",
                  "item": "https://covid19-fukui.com/about"  
                },{
                  "@type": "ListItem", 
                  "position": 7, 
                  "name": "新型コロナウイルス感染症が心配なときに",
                  "item": "https://covid19-fukui.com/flow"  
                },{
                  "@type": "ListItem", 
                  "position": 8, 
                  "name": "国内のニュース",
                  "item": "https://covid19-fukui.com/japan-news"  
                },{
                  "@type": "ListItem", 
                  "position": 9, 
                  "name": "福井県内のニュース",
                  "item": "https://covid19-fukui.com/news"  
                },{
                  "@type": "ListItem", 
                  "position": 10, 
                  "name": "福井新聞の速報RSS",
                  "item": "https://covid19-fukui.com/rss-news"  
                }]
              }
            ]
          }`,
          type: 'application/ld+json'
        }
      ]
    }
  }
})
</script>

<style lang="scss" scoped>
.MainPage {
  .Header {
    display: flex;
    flex-wrap: wrap;
    align-items: flex-end;

    @include lessThan($small) {
      flex-direction: column;
      align-items: baseline;
    }
  }

  .UpdatedAt {
    @include font-size(14);

    color: $gray-3;
    margin-bottom: 0.2rem;
  }

  .Annotation {
    @include font-size(12);

    color: $gray-3;
    @include largerThan($small) {
      margin: 0 0 0 auto;
    }
  }
  .DataBlock {
    margin: 20px -8px;

    .DataCard {
      @include largerThan($medium) {
        padding: 10px;
      }

      @include lessThan($small) {
        padding: 4px 8px;
      }
    }
  }
}
</style>
