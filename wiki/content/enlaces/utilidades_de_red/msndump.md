---
title: "MSN Dump"
date: 2018-11-22T13:35:28-03:00
---

### MSN Dump

Sniffea / interpreta lo que va por el puerto 1863, igual esto no corre mas porque el MSN no existe mas, pero lo dejo a fines de ejemplo.

```perl>
 != '0')) {
die ("you need uid 0\n");
}

# main loop
my $exitvar = '0';
while ($exitvar == '0') {

# create pcap
my $pcap = &cap_pkt;
if (!($pcap)) {
  die ("cant capture\n");
}

# drop privs
my $GID="$lowgid";
my $UID="$lowuid";
my $EGID="$lowgid $lowgid";

# -w if set
if ($opts{w}) {
  open (FILEOUT,">$opts{w}") || die ("cant open $opts{w} ($!)\n");
  fcntl(FILEOUT, F_SETFL, $flags) or die ("couldn't set nonblock for $opts{w} ($!)\n");
}

# capture loop
if (($opts{c}) && ($opts{c} =~ /(\d+)/)) {
  print "stopping after $1 packets\n";
         Net::Pcap::loop($pcap, $1, \&proc_pkt, 0);
  $exitvar = '1';
} else {
         Net::Pcap::loop($pcap, -1, \&proc_pkt, 0);
  my %stats;
  Net::Pcap::stats($pcap, \%stats);
  print "saw $stats{ps_recv} packets, dropped $stats{ps_drop}\n";
}

# free it
print "cleaning up\n";
Net::Pcap::close($pcap);
# close fh
if ($opts{w}) {
  print "wrote $opts{w}.\n";
  close FILEOUT;
}
}

# sub procs below
sub cap_pkt {

my ($pcap,$dev,$err,$mask,$net,$filter2);
my $snaplen = 14096; # seen some big im's :(
my $promisc = 1; # promisc of course
my $timeout = 0; # timeout

# file.pcap?
if ($opts{r}) {
  print "reading from '$opts{r}'\n";
  $pcap = Net::Pcap::open_offline($opts{r}, \$err);
  if (!($pcap)) {
   die("error opening $opts{r} ($err)\n");
      }
} else {

  # set dev from cmdline
  $dev = $opts{i};
  print "dumping on '$opts{i}'\n";

  # get netmask for filter
  if ((Net::Pcap::lookupnet($dev, \$net, \$mask, \$err)) == -1 ) {
          die ("Net::Pcap::lookupnet failed ($err)\n");
      }
    
  # open it
  $pcap = Net::Pcap::open_live($dev, $snaplen, $promisc, $timeout, \$err);
  if (!($pcap)) {
   die ("can't create packet fd ($err)\n");
  }
}
   
# sanity check
if (!($pcap)) {
  die ("sanity check failed - \$pcap null\n");
} elsif (!($mask)) {
  $mask = '0'; # for open_offline
}

# make filter struct
if (Net::Pcap::compile($pcap, \$filter2, $filter, 1, $mask) != '0') {
   die ("broken filter ($filter)\n");
}
# apply
Net::Pcap::setfilter($pcap, $filter2);

return $pcap;
}

sub proc_pkt {

my($user_data, $hdr, $pkt) = @_;
my ($user,$msg);

my $ip_obj = NetPacket::IP->decode(eth_strip($pkt));
#my $ip_obj = NetPacket::IP::strip($pkt);

# check if its a message (or a p2p file transfer)
# if your reading this, include 'P2P-Dest:' in your message body to avoid sniffer ;)
if (($ip_obj->{data} !~ /MSG/m) || ($ip_obj->{data} =~ /P2P-Dest:/m)) {
  ;
} else {
  print $ip_obj->{data};
  # extract goodies
  if ( (($ip_obj->{data} =~ /MSG (.*)\@(.*)/)) || (($ip_obj->{data} =~ /P4-Context: (.*)/)) ) {
   $user = "$1\@$2";
  }

  if ($ip_obj->{data} =~ /X-MMS-IM-Format:\s.*\r(.*)/s) { #\s\w+\=\w+\;\s\w+\=\w+\;\s\w+\=\w+\;\s\w+\=\w+\;\s\w+\=\w+\;(.*)/m) {
   $msg = $1;
  }

  # display if we have both
  if (($user) || ($msg))
  {
   if(!$user)
   {
    $user = "unknown user";
   }
   if (!($opts{w})) {
    print "\n----------------------------------------------------\n";
    print "src_ip($ip_obj->{src_ip}) dst_ip($ip_obj->{dest_ip})\n";
    print "TO/FROM: $user\nMESSAGE:\n$msg\n";
   } else {
    print FILEOUT "\n----------------------------------------------------\n";
    print FILEOUT "src_ip($ip_obj->{src_ip}) dst_ip($ip_obj->{dest_ip})\n";
    print FILEOUT "TO/FROM: $user\nMESSAGE: \n$msg\n\n";
   }
  }
}
}
```
