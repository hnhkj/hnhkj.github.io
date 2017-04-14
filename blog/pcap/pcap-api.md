# pcap-api

Section: C Library Functions (3)

Updated: 21 November 2003

[INDEX]

From: http://www.tcpdump.org/

## NAME

pcap - Packet Capture library

## Description

The Packet Capture library provides a high level interface to packet capture systems. All
packets on the network, even those destined for other hosts, are accessible through this
mechanism.

Edited by Rhett, SJTU, 2011


## 定義

#### #inlcude <pcap.h>

#### char errbuf[PCAP_ERRBUF_SIZE]

#### pcap_t *pcap_open_live(const char *device,int snaplen,int promisc,int to_ms,char *errbuf)


#### pcap_t *pcap_open_dead(int linktype,int snaplen)

#### pcap_t *pcap_open_offline(const char *fname,char *errbuf)

#### pcap_dumper_t *pcap_dump_open(pcap_t *p,const char *fname)

#### int pcap_setnonblok(pcap_t *p,int nonblock,char *errbuf)

#### int pcap_getnonblock(pcap_t *p,char *errbuf)

#### int pcap_findalldevs(pcap_if_t **alldevsp,char *errbuf)

#### void pcap_freealldevs(pcap_if_t *alldevs)

#### char *pcap_lookupdev(char *errbuf)

#### int pcap_lookupnet(const char *device,bpf_u_int32 *netp,bpf_u_int32 *maskp,char *errbuf)

#### int pcap_dispatch(pcap_t *p,int cnt,pcap_handler callback,u_char *user)

#### int pcap_loop(pcap_t *p,int cnt,pcap_handler callback,u_char *user)

#### void pcap_dump(u_char *user,struct pcap_pkthdr *h,u_char *sp)

#### int pcap_compile(pcap_t *p,struct bpf_program *fp,char *str,int optimize,bpf_u_int32 netmask)

#### int pcap_setfilter(pcap_t *p,struct bpf_protram *fp)

#### void pcap_freecode(struct bpf_program *)

#### const u_char *pcap_next(pcap_t *p,struct pcap_pkthdr *h)

#### int pcap_next_ex(pcap_t *p,struct pcap_pkthdr **pkt_header,const u_char **pkt_data)

#### void pcap_breakloop(pcap_t *)

#### int pcap_datalink(pcap_t *p)

#### int pcap_list_datalinks(pcap_t *p,int **dlt_buf)

#### int pcap_set_datalink(pcap_t *p,int dlt)

#### int pcap_datalink_name_to_val(const char *name)

#### const char *pcap_datalink_val_to_name(int dlt)

#### const char *pcap_datalink_val_to_descriptioin(int dlt)

#### int pcap_snapshot(pcap_t *p)

#### int pcap_is_swapped(pcap_t *p)

#### int pcap_major_version(pcap_t *p)

#### int pcap_minor_version(pcap_t *p)

#### int pcap_stats(pcap_t *p,struct pcap_stat *ps)

#### FILE *pcap_file(pcap_t *p)

#### int pcap_fileno(pcap_t *p)

#### void pcap_perror(pcap_t *p,char *prefix)

#### char *pcap_geterr(pcap_t *p)

#### char *pcap_strerror(int error)

#### const char *pcap_lib_version(void)

#### void pcpa_close(pcap_t *p)

#### void pcap_dump_flush(pcap_dumper_t *p)

#### void pcap_dump_file(pcap_dumper_t *p)

#### void pcap_dump_close(pcap_dumper_t *p)


