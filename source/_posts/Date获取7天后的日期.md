1、 DateUtils.addDays();
2、 private static Date getAfterDay(Date date, int diff) {
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(date == null ? new Date() : date);
        calendar.add(Calendar.DATE, diff);
        return calendar.getTime();
    }