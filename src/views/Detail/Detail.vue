<!-- ============================================================================================ *
 *                                                                                                *
 *                                       Xi Block Explorer                                        *
 *                                                                                                *
 * ---------------------------------------------------------------------------------------------- *
 * This file is part of the Galaxia Project - Xi Block Explorer                                   *
 * ---------------------------------------------------------------------------------------------- *
 *                                                                                                *
 * Copyright 2018 Galaxia Project Developers                                                      *
 *                                                                                                *
 * This program is free software: you can redistribute it and/or modify it under the terms of the *
 * GNU General Public License as published by the Free Software Foundation, either version 3 of   *
 * the License, or (at your option) any later version.                                            *
 *                                                                                                *
 * This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY;      *
 * without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.      *
 * See the GNU General Public License for more details.                                           *
 *                                                                                                *
 * You should have received a copy of the GNU General Public License along with this program.     *
 * If not, see <https://www.gnu.org/licenses/>.                                                   *
 *                                                                                                *
 * ============================================================================================ -->
<template>
    <div class="detail-page">

        <div class="top-section back-dark text-light">

            <!-- Transaction -->
            <div class="flex column container detail-section" v-if="result.tx">
                <div class="section-header">
                    <i class="fas fa-fw fa-exchange-alt color-accent"></i>
                    <span class="color-accent">Transaction</span>
                    <div class="spacer"></div>
                </div>
                <div class="flex column px3">
                    <div class="flex row section-row">
                        <span class="label">Hash:</span>
                        <span class="hash-value break-word">{{ result.txDetails.hash }}</span>
                    </div>
                    <div class="flex row section-row">
                        <span class="label">Confirmations:</span>
                        <span>{{ result.confirmations }}</span>
                    </div>
                    <div class="flex row section-row">
                        <span class="label">Fee:</span>
                        <span>{{ fromAtomic(result.txDetails.fee) }} {{ coinConfig.coinTicker}}</span>
                    </div>
                    <div class="flex row section-row">
                        <span class="label">Output Sum:</span>
                        <span>{{ fromAtomic(result.txDetails.amount_out) }} {{ coinConfig.coinTicker}}</span>
                    </div>
                    <div class="flex row section-row">
                        <span class="label">Mixin:</span>
                        <span>{{ result.txDetails.mixin }}</span>
                    </div>
                    <div class="flex row section-row">
                        <span class="label">Size:</span>
                        <span>{{ result.txDetails.size }}</span>
                    </div>
                    <div class="flex row section-row" v-if="result.txDetails.paymentId">
                        <span class="label">Payment Id:</span>
                        <span>{{ result.txDetails.paymentId }}</span>
                    </div>
                    <div class="flex row section-row" v-if="result.tx.unlockHeight || result.tx.unlockTime">
                        <span class="label">Unlock {{ result.tx.unlockTime ? 'Time' : 'Height' }}:</span>
                        <span>
                            {{ result.tx.unlockHeight || result.tx.unlockTime }}
                            <i v-if="!result.tx.unlocked" class="fas fa-fw fa-lock lock-icon"></i>
                        </span>

                    </div>
                </div>
            </div>

            <!-- Block -->
            <div class="flex column container detail-section" v-else-if="result.block">
                <div class="section-header">
                    <i class="fas fa-fw fa-cube color-accent"></i>
                    <span class="color-accent">Block</span>
                    <div class="spacer"></div>
                </div>
                <div class="flex column px3">
                    <div class="flex row section-row">
                        <span class="label">Hash:</span>
                        <span class="hash-value break-word">{{ result.block.hash }}</span>
                    </div>
                    <div class="flex row section-row">
                        <span class="label">Height:</span>
                        <span class="value">{{ result.block.height }}</span>
                        <span class="label" v-if="result.block.difficulty">Difficulty:</span>
                        <span class="value" v-if="result.block.difficulty">{{ result.block.difficulty }}</span>
                    </div>
                    <div class="flex row section-row">
                        <span class="label">Timestamp:</span>
                        <span class="value">{{ result.timestamp }}</span>
                        <span class="label" v-if="result.block.blockSize">Size:</span>
                        <span class="value" v-if="result.block.blockSize">{{ result.block.blockSize }}</span>
                    </div>
                    <div class="flex row section-row">
                        <span class="label">TX Count:</span>
                        <span class="value">{{ result.block.tx_count || result.block.transactions.length }}</span>
                        <span class="label" v-if="result.block.penalty || result.block.penalty == 0">Penalty:</span>
                        <span class="value" v-if="result.block.penalty || result.block.penalty == 0">{{ result.block.penalty }}</span>
                    </div>
                    <div class="flex row section-row" v-if="result.block.transactionsCumulativeSize">
                        <span class="label">TX Size:</span>
                        <span class="value">{{ result.block.transactionsCumulativeSize }}</span>
                        <span class="label" v-if="result.block.baseReward">Base Reward:</span>
                        <span class="value" v-if="result.block.baseReward">{{ result.block.baseReward }}</span>
                    </div>
                    <div class="flex row section-row" v-if="result.block.totalFeeAmount || result.block.totalFeeAmount == 0">
                        <span class="label">TX Fees:</span>
                        <span class="value">{{ fromAtomic(result.block.totalFeeAmount) }} {{ coinConfig.coinTicker }}</span>
                        <span class="label" v-if="result.block.reward">Block Reward:</span>
                        <span class="value" v-if="result.block.reward">{{ result.block.reward }}</span>
                    </div>
                </div>
            </div>
        </div>

        <!-- Search -->
        <search></search>

        <!-- Loading -->
        <page-loading v-if="loading"></page-loading>

        <!-- Error Message -->
        <page-error v-if="errorMsg" :errorMsg="errorMsg"></page-error>

        <!-- Block -->
        <div class="flex column container detail-section" v-if="result.tx && result.block">
            <div class="section-header">
                <i class="fas fa-fw fa-cube"></i>
                <span>Block</span>
                <div class="spacer"></div>
            </div>
            <div class="flex column px3">
                <div class="flex row section-row">
                    <span class="label">Hash:</span>
                    <router-link class="detail-link hash-value break-word"
                        :to="{ name: 'detail', params: { param: result.block.hash }}">
                        {{ result.block.hash }}
                    </router-link>
                </div>
                <div class="flex row section-row">
                    <span class="label">Height:</span>
                    <span class="value">{{ result.block.height }}</span>
                    <span class="label" v-if="result.block.difficulty">Difficulty:</span>
                    <span class="value" v-if="result.block.difficulty">{{ result.block.difficulty }}</span>
                </div>
                <div class="flex row section-row">
                    <span class="label">Timestamp:</span>
                    <span class="value">{{ result.timestamp }}</span>
                    <span class="label" v-if="result.block.blockSize">Size:</span>
                    <span class="value" v-if="result.block.blockSize">{{ result.block.blockSize }}</span>
                </div>
                <div class="flex row section-row">
                    <span class="label">TX Count:</span>
                    <span class="value">{{ result.block.tx_count || result.block.transactions.length }}</span>
                    <span class="label" v-if="result.block.penalty || result.block.penalty == 0">Penalty:</span>
                    <span class="value" v-if="result.block.penalty || result.block.penalty == 0">{{ result.block.penalty }}</span>
                </div>
                <div class="flex row section-row" v-if="result.block.transactionsCumulativeSize">
                    <span class="label">TX Size:</span>
                    <span class="value">{{ result.block.transactionsCumulativeSize }}</span>
                    <span class="label" v-if="result.block.baseReward">Base Reward:</span>
                    <span class="value" v-if="result.block.baseReward">{{ result.block.baseReward }}</span>
                </div>
                <div class="flex row section-row" v-if="result.block.totalFeeAmount || result.block.totalFeeAmount == 0">
                    <span class="label">TX Fees:</span>
                    <span class="value">{{ fromAtomic(result.block.totalFeeAmount) }} {{ coinConfig.coinTicker }}</span>
                    <span class="label" v-if="result.block.reward">Block Reward:</span>
                    <span class="value" v-if="result.block.reward">{{ result.block.reward }}</span>
                </div>
            </div>
        </div>

        <!-- Tx Inputs -->
        <div class="flex column container detail-section" v-if="result.tx && result.tx.vin">
            <div class="section-header">
                <i class="fas fa-fw fa-sign-in-alt"></i>
                <span>Inputs</span>
                <span>({{ result.tx.vin.length }})</span>
                <div class="spacer"></div>
            </div>
            <div class="table-row header">
                <span class="col amount">Amount</span>
                <span class="col block-hash">Key</span>
            </div>
            <div class="table-row" v-for="(input, index) in result.tx.vin" :key="index">
                <span v-if="input.value.amount" class="col amount">{{ fromAtomic(input.value.amount) }} {{ coinConfig.coinTicker }}</span>
                <span v-else class="col amount">-</span>
                <span v-if="input.value.k_image" class="col block-hash mono break-word">{{ input.value.k_image }}</span>
                <span v-else class="col block-hash">Block Reward</span>
            </div>
        </div>

        <!-- Tx Outputs -->
        <div class="flex column container detail-section" v-if="result.tx && result.tx.vout">
            <div class="section-header">
                <i class="fas fa-fw fa-sign-out-alt"></i>
                <span>Outputs</span>
                <span>({{ result.tx.vout.length }})</span>
                <div class="spacer"></div>
            </div>
            <div class="table-row header">
                <span class="col amount">Amount</span>
                <span class="col block-hash">Key</span>
            </div>
            <div class="table-row" v-for="(output, index) in result.tx.vout" :key="index">
                <span class="col amount">{{ fromAtomic(output.amount) }} {{ coinConfig.coinTicker }}</span>
                <span class="col block-hash mono break-word">{{ output.target.data.key }}</span>
            </div>
        </div>

        <!-- Block Transactions -->
        <div class="flex column container detail-section" v-if="result.block && !result.tx">
            <div class="section-header">
                <i class="fas fa-fw fa-exchange-alt"></i>
                <span>Transactions</span>
                <span>({{ result.block.transactions.length }})</span>
                <div class="spacer"></div>
            </div>
            <div class="table-row header">
                <span class="col tx-hash">TX Hash</span>
                <span class="col tx-amount">Amount</span>
                <span class="col tx-fee">Fee</span>
                <span class="col tx-size">Size</span>
            </div>
            <div class="table-row" v-for="tx in result.block.transactions" :key="tx.hash">
                <router-link class="col tx-hash detail-link mono break-word"
                    :to="{ name: 'detail', params: { param: tx.hash }}">
                    {{ tx.hash }}
                </router-link>
                <span class="col tx-amount">{{ fromAtomic(tx.amount_out) }} {{ coinConfig.coinTicker }}</span>
                <span class="col tx-fee">{{ fromAtomic(tx.fee) }}  {{ coinConfig.coinTicker }}</span>
                <span class="col tx-size">{{ tx.size }}</span>
            </div>
        </div>
        <div class="spacer" v-if="!loading && !errorMsg"></div>
        <copyright></copyright>
    </div>
</template>

<script>
import { mapActions, mapGetters, mapState } from 'vuex';
import moment from 'moment';

export default {
    name: 'detail',
    props: {
        param: undefined
    },
    data () {
        return {
            loading: true,
            errorMsg: '',
            result: {}
        };
    },
    watch: {
        'param': function (id) {
            this.doSearch();
        }
    },
    mounted: function () {

        this.doSearch();
    },
    computed: {
        ...mapState({
            coinConfig: state => state.explorer.coinConfig,
            dateFormat: state => state.explorer.dateFormat,
            blockHeight: state => state.explorer.blockHeight,
            blockService: state => state.explorer.blockService
        }),
    },
    methods: {
        doSearch: function () {

            this.loading = true;
            this.errorMsg = '';
            this.result = {};

            // Return if no param.
            if (!this.param) {

                return this.setError('');
            }

            // Check if hash.
            if (this.param.length == 64) {

                return this.findHash(this.param);
            }

            // Validate height.
            if (isNaN(this.param)) {

                return this.setError('Invalid  hash or height');
            }

            // Get block hash from height.
            let height = parseInt(this.param);
            return this.findBlock(height);
        },
        localTimestamp (timestamp) {

            return moment.unix(timestamp).format(this.dateFormat);
        },
        fromAtomic (amount) {

            return (amount / this.coinConfig.coinUnits).toFixed(this.coinConfig.decimals);
        },
        unlockHeight (height, unlockBlocks) {

            return height + unlockBlocks;
        },
        findBlock (height) {

            this.blockService.getBlockHash(height).then((hash) => {

                this.findHash(hash);
            }).catch((err) => {

                this.setError('No results found');
            });
        },
        findHash (hash) {

            return this.blockService.find(hash).then((result) => {

                this.setResult(result);
            }).catch((err) => {

                this.setError('No results found');
            });
        },
        setResult (result) {

            if (!result || !result.block) {

                return this.setError('No results found');
            }

            // Set calculated properties.
            result.confirmations = this.blockHeight - result.block.height;
            result.timestamp = this.localTimestamp(result.block.timestamp);

            // Set extra transaction properties.
            if (result.tx) {

                // Set unlock_time in case it's not being used.
                result.tx.unlock_time = result.tx.unlock_time || 0;

                // Use unlock_time as block height if it's less than maxBlockHeight
                if (result.tx.unlock_time < this.coinConfig.maxBlockHeight) {

                    // Use block height if unlockHeight is before tx height.
                    result.tx.unlockHeight = (result.tx.unlock_time > result.block.height) ?
                        result.tx.unlock_time : result.block.height;
                    result.tx.unlocked = this.blockHeight >= result.tx.unlockHeight;
                } else {

                    // Otherwise unlock_time as a timestamp.
                    let unlockTime = (result.tx.unlock_time > result.block.timestamp) ?
                        moment.unix(result.tx.unlock_time) : moment.unix(result.block.timestamp);

                    if (unlockTime.isValid()) {

                        result.tx.unlockTime = unlockTime.format(this.dateFormat);
                        result.tx.unlocked = moment().isSameOrAfter(unlockTime);
                    }
                }
            }

            // Set result.
            this.loading = false;
            this.errorMsg = '';
            this.result = result;
        },
        setError (errorMsg) {

            // Not a valid height.
            this.loading = false;
            this.errorMsg = errorMsg;
            this.result = {};
        }
    }
};
</script>

<style scoped>
.detail-page {
    max-width: 100vw;
    display: flex;
    flex-direction: column;
    flex-grow: 1;
    flex-shrink: 0;
    box-sizing: border-box;
    align-items: center;
}
.detail-section {
    margin-bottom: 16px;
    -webkit-animation: fadein 0.5s; /* Safari, Chrome and Opera > 12.1 */
    -moz-animation: fadein 0.5s; /* Firefox < 16 */
    -ms-animation: fadein 0.5s; /* Internet Explorer */
    -o-animation: fadein 0.5s; /* Opera < 12.1 */
    animation: fadein 0.5s;
}
.section-header {
    font-size: 18px;
}
.section-header span {
    font-size: 22px;
}
.section-row {
    flex-wrap: wrap;
    padding-bottom: 8px;
}
.label {
    width: 140px;
    font-weight: 600;
    padding-right: 8px;
}
.value {
    width: 400px;
}
.lock-icon {
    padding-left: 8px;
    font-size: 14px;
    color: #FBB13C;
}
.detail-link {
    font-weight: 400;
    color: #6A6B70;
    color: #2780E3 !important;
    text-decoration: none;
}
.col {
    flex-shrink: 0;
}
.col.amount {
    min-width: 110px;
    width: 140px;
    flex-grow: 1;
    padding-right: 16px;
}
.col.block-hash {
    max-width: calc(100vw - 32px) !important;
    flex-grow: 3;
}
.col.tx-amount {
    width: 150px;
}
.col.tx-fee {
    width: 120px;
}
.col.tx-size {
    width: 120px;
}
.col.tx-hash {
    max-width: calc(100vw - 32px) !important;
    flex-grow: 1;
}
.table-row {
    /*box-shadow: 0px -1px 0px rgba(153,153,153,0.3) inset;*/
    padding: 8px 16px;
    width: 100%;
    display: flex;
    flex-grow: 0;
    flex-shrink: 0;
    box-sizing: border-box;
    flex-direction: row;
    flex-wrap: wrap;
    color: #1A1B20;
}
.table-row.header {
    font-weight: 600;
    color: #2A2B30;
}
.right {
    text-align: right !important;
}
.hash-value {
    max-width: calc(100vw - 32px) !important;
}
@media all and (orientation:portrait) {

}
@media all and (max-width: 599px) {
    .section-row {
        flex-direction: column !important;
        align-items: flex-start !important;
    }
    .lock-icon {
        padding-left: 0px;
    }
    .col.tx-hash {
        min-width: 100px;
    }
    .table-row {
        max-width: 100vw;
    }
}
@media all and (min-width: 600px) {
    .col.block-hash {
        flex-grow: 3;
        min-width: 600px;
    }
}
@media all and (min-width: 1600px) {

}
@media all and (min-width: 1800px) {

}
@media all and (min-width: 2200px) {
    .col.amount {
        flex-grow: 0;
        flex-shrink: 0;
        min-width: 400px;
        width: 400px;
    }
    .label {
        min-width: 260px;
        max-width: 260px;
        width: 260px;
        font-weight: 600;
        padding-right: 8px;
    }
}
</style>
