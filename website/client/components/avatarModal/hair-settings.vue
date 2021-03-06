<template lang="pug">
  #hair.section.customize-section
    sub-menu.text-center(:items="hairSubMenuItems", :activeSubPage="activeSubPage", @changeSubPage="changeSubPage($event)")

    #hair-color(v-if='activeSubPage === "color"')
      customize-options(
        :items="freeHairColors",
        :currentValue="user.preferences.hair.color"
      )

      div(v-if='editing && set.key !== "undefined"', v-for='set in seasonalHairColors')
        customize-options(
          :items='set.options',
          :currentValue="user.preferences.skin",
          :fullSet='!hideSet(set) && !userOwnsSet("hair", set.keys, "color")',
          @unlock='unlock(`hair.color.${set.keys.join(",hair.color.")}`)'
        )

    #style(v-if='activeSubPage === "style"')
      div(v-for='set in styleSets')
        customize-options(
          :items='set.options',
          :fullSet='set.fullSet',
          @unlock='set.unlock()'
        )
    #bangs(v-if='activeSubPage === "bangs"')
      customize-options(
        :items='hairBangs',
        :currentValue="user.preferences.hair.bangs"
      )
    #facialhair(v-if='activeSubPage === "facialhair"')
      customize-options(
        v-if='editing',
        :items='mustacheList'
      )
      customize-options(
        v-if='editing',
        :items='beardList',
        :fullSet='isPurchaseAllNeeded("hair", ["baseHair5", "baseHair6"], ["mustache", "beard"])',
        @unlock='unlock(`hair.mustache.${baseHair5Keys.join(",hair.mustache.")},hair.beard.${baseHair6Keys.join(",hair.beard.")}`)'
      )
</template>

<script>
  import appearance from 'common/script/content/appearance';
  import {subPageMixin} from '../../mixins/subPage';
  import {userStateMixin} from '../../mixins/userState';
  import {avatarEditorUtilies} from '../../mixins/avatarEditUtilities';
  import subMenu from './sub-menu';
  import customizeOptions from './customize-options';
  import gem from 'assets/svg/gem.svg';
  import appearanceSets from 'common/script/content/appearance/sets';
  import groupBy from 'lodash/groupBy';

  const hairColorBySet = groupBy(appearance.hair.color, 'set.key');
  const freeHairColorKeys = hairColorBySet[undefined].map(s => s.key);

  export default {
    props: [
      'editing',
    ],
    components: {
      subMenu,
      customizeOptions,
    },
    mixins: [
      subPageMixin,
      userStateMixin,
      avatarEditorUtilies,
    ],
    data () {
      return {
        freeHairColorKeys,
        icons: Object.freeze({
          gem,
        }),
        baseHair1: [1, 3],
        baseHair2Keys: [2, 4, 5, 6, 7, 8],
        baseHair3Keys: [9, 10, 11, 12, 13, 14],
        baseHair4Keys: [15, 16, 17, 18, 19, 20],
        baseHair5Keys: [1, 2],
        baseHair6Keys: [1, 2, 3],
      };
    },
    computed: {
      hairSubMenuItems () {
        const items = [
          {
            id: 'color',
            label: this.$t('color'),
          },
          {
            id: 'bangs',
            label: this.$t('bangs'),
          },
          {
            id: 'style',
            label: this.$t('style'),
          },
        ];

        if (this.editing) {
          items.push({
            id: 'facialhair',
            label: this.$t('facialhair'),
          });
        }

        return items;
      },
      freeHairColors () {
        return freeHairColorKeys.map(s => this.mapKeysToFreeOption(s, 'hair', 'color'));
      },
      seasonalHairColors () {
        // @TODO: For some resonse when I use $set on the user purchases object, this is not recomputed. Hack for now
        let backgroundUpdate = this.backgroundUpdate; // eslint-disable-line

        let seasonalHairColors = [];
        for (let key in hairColorBySet) {
          let set = hairColorBySet[key];

          let keys = set.map(item => {
            return item.key;
          });

          let options = keys.map(optionKey => {
            const option = this.mapKeysToOption(optionKey, 'hair', 'color', key);
            return option;
          });

          let text = this.$t(key);
          if (appearanceSets[key] && appearanceSets[key].text) {
            text = appearanceSets[key].text();
          }

          let compiledSet = {
            key,
            options,
            keys,
            text,
          };
          seasonalHairColors.push(compiledSet);
        }

        return seasonalHairColors;
      },
      premiumHairColors () {
        // @TODO: For some resonse when I use $set on the user purchases object, this is not recomputed. Hack for now
        let backgroundUpdate = this.backgroundUpdate; // eslint-disable-line
        let keys = this.premiumHairColorKeys;
        let options = keys.map(key => {
          return this.mapKeysToOption(key, 'hair', 'color');
        });
        return options;
      },
      baseHair2 () {
        // @TODO: For some resonse when I use $set on the user purchases object, this is not recomputed. Hack for now
        let backgroundUpdate = this.backgroundUpdate; // eslint-disable-line
        let keys = this.baseHair2Keys;
        let options = keys.map(key => {
          return this.mapKeysToOption(key, 'hair', 'base');
        });
        return options;
      },
      baseHair3 () {
        // @TODO: For some resonse when I use $set on the user purchases object, this is not recomputed. Hack for now
        let backgroundUpdate = this.backgroundUpdate; // eslint-disable-line
        let keys = this.baseHair3Keys;
        let options = keys.map(key => {
          const option = this.mapKeysToOption(key, 'hair', 'base');
          return option;
        });
        return options;
      },
      baseHair4 () {
        // @TODO: For some resonse when I use $set on the user purchases object, this is not recomputed. Hack for now
        let backgroundUpdate = this.backgroundUpdate; // eslint-disable-line
        let keys = this.baseHair4Keys;
        let options = keys.map(key => {
          return this.mapKeysToOption(key, 'hair', 'base');
        });
        return options;
      },
      baseHair5 () {
        // @TODO: For some resonse when I use $set on the user purchases object, this is not recomputed. Hack for now
        let backgroundUpdate = this.backgroundUpdate; // eslint-disable-line
        let keys = this.baseHair5Keys;
        let options = keys.map(key => {
          return this.mapKeysToOption(key, 'hair', 'mustache');
        });
        return options;
      },
      baseHair6 () {
        // @TODO: For some resonse when I use $set on the user purchases object, this is not recomputed. Hack for now
        let backgroundUpdate = this.backgroundUpdate; // eslint-disable-line
        let keys = this.baseHair6Keys;
        let options = keys.map(key => {
          return this.mapKeysToOption(key, 'hair', 'beard');
        });
        return options;
      },
      hairBangs () {
        const none = this.mapKeysToFreeOption(0, 'hair', 'bangs');
        none.none = true;

        const options = [1, 2, 3, 4].map(s => this.mapKeysToFreeOption(s, 'hair', 'bangs'));

        return [none, ...options];
      },
      mustacheList () {
        const noneOption = this.mapKeysToFreeOption(0, 'hair', 'mustache');
        noneOption.none = true;

        return [noneOption, ...this.baseHair5];
      },
      beardList () {
        const noneOption = this.mapKeysToFreeOption(0, 'hair', 'beard');
        noneOption.none = true;

        return [noneOption, ...this.baseHair6];
      },
      styleSets () {
        const sets = [];

        const emptyHairBase = {
          ...this.mapKeysToFreeOption(0, 'hair', 'base'),
          none: true,
        };

        if (this.editing) {
          sets.push({
            fullSet: !this.userOwnsSet('hair', this.baseHair3Keys, 'base'),
            unlock: () => this.unlock(`hair.base.${this.baseHair3Keys.join(',hair.base.')}`),
            options: [
              emptyHairBase,
              ...this.baseHair3,
            ],
          });

          sets.push({
            fullSet: !this.userOwnsSet('hair', this.baseHair4Keys, 'base'),
            unlock: () => this.unlock(`hair.base.${this.baseHair4Keys.join(',hair.base.')}`),
            options: [
              ...this.baseHair4,
            ],
          });
        }

        sets.push({
          options: [
            emptyHairBase,
            ...this.baseHair1.map(key => this.mapKeysToFreeOption(key, 'hair', 'base')),
          ],
        });

        if (this.editing) {
          sets.push({
            fullSet: !this.userOwnsSet('hair', this.baseHair2Keys, 'base'),
            unlock: () => this.unlock(`hair.base.${this.baseHair2Keys.join(',hair.base.')}`),
            options: [
              ...this.baseHair2,
            ],
          });
        }

        return sets;
      },
    },
    mounted () {
      this.changeSubPage('color');
    },
    methods: {
      /**
       * Allows you to find out whether you need the "Purchase All" button or not. If there are more than 2 unpurchased items, returns true, otherwise returns false.
       * @param {string} category - The selected category.
       * @param {string[]} keySets - The items keySets.
       * @param {string[]} [types] - The items types (subcategories). Optional.
       * @returns {boolean} - Determines whether the "Purchase All" button is needed (true) or not (false).
       */
      isPurchaseAllNeeded (category, keySets, types) {
        const purchasedItemsLengths = [];
        // If item types are specified, count them
        if (types && types.length > 0) {
          // Types can be undefined, so we must check them.
          types.forEach((type) => {
            if (this.user.purchased[category][type]) {
              purchasedItemsLengths
                .push(Object.keys(this.user.purchased[category][type]).length);
            }
          });
        } else {
          let purchasedItemsCounter = 0;

          // If types are not specified, recursively
          // search for purchased items in the category
          const findPurchasedItems = (item) => {
            if (typeof item === 'object') {
              Object.values(item)
                .forEach((innerItem) => {
                  if (typeof innerItem === 'boolean' && innerItem === true) {
                    purchasedItemsCounter += 1;
                  }
                  return findPurchasedItems(innerItem);
                });
            }
            return purchasedItemsCounter;
          };

          findPurchasedItems(this.user.purchased[category]);
          if (purchasedItemsCounter > 0) {
            purchasedItemsLengths.push(purchasedItemsCounter);
          }
        }

        // We don't need to count the key sets (below)
        // if there are no purchased items at all.
        if (purchasedItemsLengths.length === 0) {
          return true;
        }

        const allItemsLengths = [];
        // Key sets must be specify correctly.
        keySets.forEach((keySet) => {
          allItemsLengths.push(Object.keys(this[keySet]).length);
        });

        // Simply sum all the length values and
        // write them into variables for the convenience.
        const allItems = allItemsLengths.reduce((acc, val) => acc + val);
        const purchasedItems = purchasedItemsLengths.reduce((acc, val) => acc + val);

        const unpurchasedItems = allItems - purchasedItems;
        return unpurchasedItems > 2;
      },
    },
  };
</script>

<style scoped>

</style>
