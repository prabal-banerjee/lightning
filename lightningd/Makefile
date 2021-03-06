#! /usr/bin/make

# Designed to be run one level up
lightningd-wrongdir:
	$(MAKE) -C .. lightningd-all

LIGHTNINGD_PROGRAM := lightningd/lightningd

ALL_PROGRAMS += $(LIGHTNINGD_PROGRAM)

lightningd-all: $(LIGHTNINGD_PROGRAM)

default: lightningd-all

# Common source we use.
LIGHTNINGD_COMMON_OBJS :=			\
	common/addr.o				\
	common/amount.o				\
	common/base32.o				\
	common/base64.o				\
	common/bech32.o				\
	common/bech32_util.o			\
	common/bigsize.o			\
	common/bip32.o				\
	common/blinding.o			\
	common/bolt11.o				\
	common/channel_id.o			\
	common/channel_config.o			\
	common/coin_mvt.o			\
	common/configdir.o			\
	common/crypto_state.o			\
	common/daemon.o				\
	common/derive_basepoints.o		\
	common/ecdh_hsmd.o			\
	common/features.o			\
	common/fee_states.o			\
	common/funding_tx.o			\
	common/gen_peer_status_wire.o		\
	common/gen_status_wire.o		\
	common/gossip_rcvd_filter.o		\
	common/hash_u5.o			\
	common/hmac.o				\
	common/htlc_state.o			\
	common/htlc_trim.o			\
	common/htlc_wire.o			\
	common/key_derive.o			\
	common/keyset.o				\
	common/io_lock.o			\
	common/json.o				\
	common/json_helpers.o			\
	common/json_stream.o			\
	common/json_tok.o			\
	common/memleak.o			\
	common/msg_queue.o			\
	common/node_id.o			\
	common/onion.o				\
	common/onionreply.o			\
	common/param.o				\
	common/penalty_base.o			\
	common/per_peer_state.o			\
	common/permute_tx.o			\
	common/pseudorand.o			\
	common/setup.o				\
	common/sphinx.o				\
	common/status_wire.o			\
	common/timeout.o			\
	common/type_to_string.o			\
	common/utils.o				\
	common/utxo.o				\
	common/version.o			\
	common/wallet.o				\
	common/wallet_tx.o			\
	common/wire_error.o			\
	common/wireaddr.o			\
	common/withdraw_tx.o			\
	hsmd/gen_hsm_wire.o

LIGHTNINGD_SRC :=				\
	lightningd/bitcoind.c			\
	lightningd/chaintopology.c		\
	lightningd/channel.c			\
	lightningd/channel_control.c		\
	lightningd/closing_control.c		\
	lightningd/coin_mvts.c			\
	lightningd/connect_control.c		\
	lightningd/onion_message.c		\
	lightningd/gossip_control.c		\
	lightningd/gossip_msg.c			\
	lightningd/hsm_control.c		\
	lightningd/htlc_end.c			\
	lightningd/htlc_set.c			\
	lightningd/invoice.c			\
	lightningd/io_loop_with_timers.c	\
	lightningd/json.c			\
	lightningd/jsonrpc.c			\
	lightningd/lightningd.c			\
	lightningd/log.c			\
	lightningd/log_status.c			\
	lightningd/memdump.c			\
	lightningd/notification.c		\
	lightningd/onchain_control.c		\
	lightningd/opening_control.c		\
	lightningd/options.c			\
	lightningd/pay.c			\
	lightningd/peer_control.c		\
	lightningd/peer_htlcs.c			\
	lightningd/ping.c			\
	lightningd/plugin.c			\
	lightningd/plugin_control.c		\
	lightningd/plugin_hook.c		\
	lightningd/subd.c			\
	lightningd/watch.c

LIGHTNINGD_SRC_NOHDR :=				\
	lightningd/signmessage.c

LIGHTNINGD_OBJS := $(LIGHTNINGD_SRC:.c=.o) $(LIGHTNINGD_SRC_NOHDR:.c=.o)

# Make sure these depend on everything.
ALL_OBJS += $(LIGHTNINGD_OBJS)

# We accumulate all lightningd/ headers in these three:
LIGHTNINGD_HEADERS_NOGEN =			\
	$(LIGHTNINGD_SRC:.c=.h)			\
	lightningd/channel_state.h

# Generated headers
LIGHTNINGD_HEADERS_GEN =			\
	lightningd/gen_channel_state_names.h

ALL_GEN_HEADERS += $(LIGHTNINGD_HEADERS_GEN)

include wallet/Makefile

# All together in one convenient var
LIGHTNINGD_HEADERS = $(LIGHTNINGD_HEADERS_NOGEN) $(LIGHTNINGD_HEADERS_GEN) $(WALLET_LIB_HEADERS)

$(LIGHTNINGD_OBJS): $(LIGHTNINGD_HEADERS)

lightningd/gen_channel_state_names.h: lightningd/channel_state.h ccan/ccan/cdump/tools/cdump-enumstr
	ccan/ccan/cdump/tools/cdump-enumstr lightningd/channel_state.h > $@

check-source: $(LIGHTNINGD_SRC:%=check-src-include-order/%) $(LIGHTNINGD_SRC_NOHDR:%=check-src-include-order/%)
check-source: $(LIGHTNINGD_HEADERS_NOGEN:%=check-hdr-include-order/%)
check-source-bolt: $(LIGHTNINGD_SRC:%=bolt-check/%) $(LIGHTNINGD_SRC_NOHDR:%=bolt-check/%) $(LIGHTNINGD_HEADERS_NOGEN:%=bolt-check/%)

check-whitespace: $(LIGHTNINGD_SRC:%=check-whitespace/%) $(LIGHTNINGD_SRC_NOHDR:%=check-whitespace/%) $(LIGHTNINGD_HEADERS_NOGEN:%=check-whitespace/%)

check-makefile: check-lightningd-makefile
check-lightningd-makefile:
	@for f in lightningd/*.h lightningd/*/*.h; do if ! echo $(LIGHTNINGD_HEADERS_NOGEN) $(LIGHTNINGD_HEADERS_GEN) "" | grep -q "$$f "; then echo $$f not mentioned in LIGHTNINGD_HEADERS_NOGEN or LIGHTNINGD_HEADERS_GEN >&2; exit 1; fi; done

lightningd/lightningd: $(LIGHTNINGD_OBJS) $(LIGHTNINGD_COMMON_OBJS) $(BITCOIN_OBJS) $(WIRE_OBJS) $(WIRE_ONION_OBJS) $(LIGHTNINGD_HANDSHAKE_CONTROL_OBJS) $(LIGHTNINGD_GOSSIP_CONTROL_OBJS) $(LIGHTNINGD_OPENING_CONTROL_OBJS) $(LIGHTNINGD_CHANNEL_CONTROL_OBJS) $(LIGHTNINGD_CLOSING_CONTROL_OBJS) $(LIGHTNINGD_ONCHAIN_CONTROL_OBJS) $(WALLET_LIB_OBJS) $(LIGHTNINGD_CONNECT_CONTROL_OBJS)

clean: lightningd-clean

lightningd-clean:
	$(RM) $(LIGHTNINGD_OBJS) $(LIGHTNINGD_JSMN_OBJS) $(LIGHTNINGD_PROGRAM) lightningd/gen*

include lightningd/test/Makefile
